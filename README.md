# pip-tools

# Setup

Need to make a virtualenv to start. I decided to try [`direnv`](https://direnv.net/).

```shell
echo "layout python" > .envrc
direnv allow .envrc
```

The usual way would be to instead:

```shell
python -m venv .env_name
source .env_name/bin/activate
```

Make a dummy project with `__init__.py` containing a `__version__`, `__main__.py` which imports from `requests`.

# Requirements

Create `requirements.in` whose 

```shell
cat requirements.in 
requests~=2.0
```

Can we lock this?

```shell
pip install pip-tools
pip-compile
```

No hashes in the output!

```
pip-compile --generate-hashes
```

That's better. Please update my virtualenv.

```
$ pip-sync -a
Would install:
  certifi==2021.10.8
  charset-normalizer==2.0.10
  idna==3.3
  requests==2.27.1
  urllib3==1.26.8
Would you like to proceed with these changes? [y/N]: y
Collecting certifi==2021.10.8
  Using cached certifi-2021.10.8-py2.py3-none-any.whl (149 kB)
Collecting charset-normalizer==2.0.10
  Using cached charset_normalizer-2.0.10-py3-none-any.whl (39 kB)
Collecting idna==3.3
  Using cached idna-3.3-py3-none-any.whl (61 kB)
Collecting requests==2.27.1
  Using cached requests-2.27.1-py2.py3-none-any.whl (63 kB)
Collecting urllib3==1.26.8
  Using cached urllib3-1.26.8-py2.py3-none-any.whl (138 kB)
Installing collected packages: urllib3, idna, charset-normalizer, certifi, requests
Successfully installed certifi-2021.10.8 charset-normalizer-2.0.10 idna-3.3 requests-2.27.1 urllib3-1.26.8
```

I want to add dev-dependencies now.

```shell
$ cat requirements-dev.in 
-r requirements.in
black~=21.12b0
```

By default, `pip-compile` only reads from `requirements.in` and writes to `requirements.txt`. Process both separately.

```shell
pip-compile --generate-hashes requirements.in      # creates requrements.txt
pip-compile --generate-hashes requirements-dev.in  # creates requirements-dev.txt
pip-sync -a requirements-dev.txt
```

Cool. But if we're being paranoid, we should also pin pip-tools and its dependencies!

```shell
echo "pip-tools~=6.4" >> requirements-dev.in
pip-compile --generate-hashes requirements-dev.in
```

This works, but warns me:

```
# WARNING: The following packages were not pinned, but pip requires them to be
# pinned when the requirements file includes hashes. Consider using the --allow-unsafe flag.
# pip
# setuptools
The generated requirements file may be rejected by pip install. See # WARNING lines for details
```

Readme says:

> Note: pip-sync will not upgrade or uninstall packaging tools like setuptools, pip, or pip-tools itself. Use python -m pip install --upgrade to upgrade those packages.

I think I was able to pin (?) pip and setuptools with

```
pip-compile --generate-hashes requirements-dev.in --allow-unsafe
```

But `--allow-unsafe` is scary. It is apparently going to become the default behaviour in future versions of pip-tools though? Weird. Weirer still, `pip-sync` would happily upgrade `pip` and `setuptools` in my venv!

```
$ pip-compile --generate-hashes --allow-unsafe -U requirements-dev.in
$ pip-sync -a requirements-dev.txt
Would install:
  pip==21.3.1
  setuptools==60.5.0
Would you like to proceed with these changes? [y/N]: y
Collecting pip==21.3.1
  Using cached pip-21.3.1-py3-none-any.whl (1.7 MB)
Collecting setuptools==60.5.0
  Using cached setuptools-60.5.0-py3-none-any.whl (958 kB)
Installing collected packages: setuptools, pip
  Attempting uninstall: setuptools
    Found existing installation: setuptools 57.4.0
    Uninstalling setuptools-57.4.0:
      Successfully uninstalled setuptools-57.4.0
  Attempting uninstall: pip
    Found existing installation: pip 21.2.3
    Uninstalling pip-21.2.3:
      Successfully uninstalled pip-21.2.3
Successfully installed pip-21.3.1 setuptools-60.5.0

$ which pip
~/workspace/hello-pip-tools/.direnv/python-3.10.1/bin/pip
$ pip --version
pip 21.3.1 from /home/dmr/workspace/hello-pip-tools/.direnv/python-3.10.1/lib64/python3.10/site-packages/pip (python 3.10)
```

# Editable install

Nice. Can I install the package in editable mode?

```
pip install -e .
ERROR: File "setup.py" or "setup.cfg" not found. Directory cannot be installed in editable mode: /home/dmr/workspace/hello-pip-tools
```

I found this quite confusing.
I think `requirements.in` is meant to be used only if you don't have a `setup.py/cfg`?

Now invoke with `pip-compile setup.cfg --generate-hashes`.

Setup.cfg reference hard to find. It's here: https://setuptools.pypa.io/en/latest/userguide/declarative_config.html#specifying-values but doesn't describe the options' meaning. Have to go to https://setuptools.pypa.io/en/latest/userguide/keywords.html for that. And that's only for new ones added by setuptools? I think you might have to consult https://packaging.python.org/en/latest/guides/distributing-packages-using-setuptools/#setup-args ?

It's incredibly confusing and I can see the appeal of a single tool that Just Does Everything.

Error messages when I messed up setup.cfg syntax were unhelpful. (CalledProcessError traceback)

Ended up with something like 

```
pip-compile setup.cfg --generate-hashes --allow-unsafe -o requirements.txt
pip-compile setup.cfg --generate-hashes --extra dev,test,lint,meta --allow-unsafe -o requirements-dev.txt
```

`pip-sync` will try to undo an editable install, grrr. Maybe that is just fine? I'm not sure.

`pip-compile` doesn't give you a good sense of what's changed. It just prints out the lock file.

The annotations don't export a full chain of dependencies back to the requirements; just "A needed by B1, B2, B3". I.e. one edge deep.

### TL;DR:
Good:
- simple tools
- supports multiple `extras_requires`. I'm not sure if we actually want this
- Was happy with the virtualenv I gave it!
- pip-sync automatically verified hashes if they were present in `requirements*.txt` (I guess this is just pip behaviour)
- okay integration with pycharm: it's just a vanilla venv
 - pycharm couldn't autodetect the interpreter that `direnv` had created though.

Neutral:
- Pip-compile dumps the lockfile to stdout as well as writing it. Rely on diff tools to see what's changed

Bad:
- have to opt-in to hashing
- have to work how which set of flags you actually want
- No useful information in the error message if you screw up setup.cfg syntax
 - though maybe pycharm would have spotted some of these problems? I just used `vim` directly.
- `pip-sync` undoes my editable install!
 - I couldn't work out how to say "editable install the package itself" in setup.cfg. See also https://github.com/jazzband/pip-tools/issues/20
- annotations in the output only show you that package A depends on package B; you have to then lookup package B.
- no easy way to see what updates are available

Other:
- requirements.in versus setup.py/cfg was not obvious!!
- one lock file per "environment"
 - e.g. different python version/interpreter, different architecture, different `extras_requires` groups
 - output not used in the current environment is omitted from the lockfile
- feels like it needs some configuration to get right
- Unclear if it will meta-manage pip, setuptools and pip-tools
 - Seemed to work but docs seemed to think otherwise.

## TODO 
I've used `~=` pinning here. That allows updates to the smallest unit of change AFAICS. Should I pin to a specific version instead?
Note that poetry has a slightly more flexible operator `^=` which allows any kind of semver-compatible upgrade.





