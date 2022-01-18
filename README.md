# pipenv

Let's try `direnv` again.

```shell
dmr on titan in workspace/hello-pipenv
2022-01-17 13:47:35 ✔  $ echo layout pipenv >> .envrc
direnv: error /home/dmr/workspace/hello-pipenv/.envrc is blocked. Run `direnv allow` to approve its content

dmr on titan in workspace/hello-pipenv
2022-01-17 13:48:02 ✔  $ direnv allow
direnv: loading ~/workspace/hello-pipenv/.envrc
direnv: No Pipfile found.  Use `pipenv` to create a `Pipfile` first.
```

Fair enough. Copy the example from the basic how-to. Looks like I have to configure it to fetch from PyPI. This isn't explained???

Was fairly straightforward  to write a pipfile. Then I think direnv did a `pipenv install`:

```shell
2022-01-17 13:57:34 ✔  $ cd -
/home/dmr/workspace/hello-pipenv
direnv: loading ~/workspace/hello-pipenv/.envrc
Creating a virtualenv for this project...
Pipfile: /home/dmr/workspace/hello-pipenv/Pipfile
Using /usr/bin/python3 (3.10.1) to create virtualenv...
⠹ Creating virtual environment...created virtual environment CPython3.10.1.final.0-64 in 145ms
  creator CPython3Posix(dest=/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(extra_search_dir=/usr/share/python-wheels,download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/home/dmr/.local/share/virtualenv)
    added seed packages: pip==21.2.3, setuptools==57.4.0, wheel==0.36.2
  activators BashActivator,CShellActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator

✔ Successfully created virtual environment! 
Virtualenv location: /home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB
Pipfile.lock not found, creating...
Locking [dev-packages] dependencies...
Building requirements...
Resolving dependencies...
⠦ Locking...direnv: ([/usr/bin/direnv export bash]) is taking a while to execute. Use CTRL-C to give up.
✔ Success! 
Locking [packages] dependencies...
Building requirements...
Resolving dependencies...
✔ Success! 
Updated Pipfile.lock (1321e0)!
Installing dependencies from Pipfile.lock (1321e0)...
An error occurred while installing pathspec==0.9.0 --hash=sha256:7d15c4ddb0b5c802d161efc417ec1a2558ea2653c2e8ad9c19098201dc1c993a --hash=sha256:e564499435a2673d586f6b2130bb5b95f04a3ba06f81b8f895b651a3c76aabb1! Will try again.
An error occurred while installing platformdirs==2.4.1; python_version >= '3.7' --hash=sha256:1d7385c7db91728b83efd0ca99a5afb296cab9d0ed8313a45ed8ba17967ecfca --hash=sha256:440633ddfebcc36264232365d7840a970e75e1018d15b4327d11f91909045fda! Will try again.
  🐍   ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 30/30 — 00:00:05
Installing initially failed dependencies...
  ☤  ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 2/2 — 00:00:00
To activate this project's virtualenv, run pipenv shell.
Alternatively, run a command inside the virtualenv with pipenv run.
direnv: export +PIPENV_ACTIVE +VIRTUAL_ENV ~PATH

```

I didn't like `An error occurred... Will try again.`. Is this my problem? A network problem? A package incompatibility that's normal behaviour?

Pipenv seemed more than happy to install pip-env.

`pipenv shell` is clever enough to not double activate.

`direnv` is quite pleasant to use (but was also true of vanilla virtualenv with pip-tools).

Was slightly less effort to get myself setup 
 - install pipenv; write Pipfile; pipenv install;
 - versus create venv; install pip-tools; write setup.cfg; pip-compile; pip-sync.

For "I want to clone a project that's already been configured":
 - install pipenv; pipenv install
 - create venv; install pip-tools; pip-sync.

Let's try an editable install. According to the docs it should just be https://pipenv.pypa.io/en/latest/basics/#editable-dependencies-e-g-e

```
pipenv install --dev -e .
```

```shell
Installing -e ....
Traceback (most recent call last):
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/_vendor/packaging/requirements.py", line 102, in __init__
    req = REQUIREMENT.parseString(requirement_string)
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/_vendor/pyparsing.py", line 1654, in parseString
    raise exc
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/_vendor/pyparsing.py", line 1644, in parseString
    loc, tokens = self._parse( instring, 0 )
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/_vendor/pyparsing.py", line 1402, in _parseNoCache
    loc,tokens = self.parseImpl( instring, preloc, doActions )
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/_vendor/pyparsing.py", line 3417, in parseImpl
    loc, exprtokens = e._parse( instring, loc, doActions )
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/_vendor/pyparsing.py", line 1402, in _parseNoCache
    loc,tokens = self.parseImpl( instring, preloc, doActions )
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/_vendor/pyparsing.py", line 3739, in parseImpl
    return self.expr._parse( instring, loc, doActions, callPreParse=False )
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/_vendor/pyparsing.py", line 1402, in _parseNoCache
    loc,tokens = self.parseImpl( instring, preloc, doActions )
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/_vendor/pyparsing.py", line 3400, in parseImpl
    loc, resultlist = self.exprs[0]._parse( instring, loc, doActions, callPreParse=False )
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/_vendor/pyparsing.py", line 1406, in _parseNoCache
    loc,tokens = self.parseImpl( instring, preloc, doActions )
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/_vendor/pyparsing.py", line 2711, in parseImpl
    raise ParseException(instring, loc, self.errmsg, self)
pkg_resources._vendor.pyparsing.ParseException: Expected W:(abcd...) (at char 0), (line:1, col:1)

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/vendor/requirementslib/models/requirements.py", line 966, in _parse_name_from_line
    self._requirement = init_requirement(self.line)
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/vendor/requirementslib/models/utils.py", line 197, in init_requirement
    req = Requirement.parse(name)
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/__init__.py", line 3154, in parse
    req, = parse_requirements(s)
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/__init__.py", line 3099, in parse_requirements
    yield Requirement(line)
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/__init__.py", line 3109, in __init__
    super(Requirement, self).__init__(requirement_string)
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pkg_resources/_vendor/packaging/requirements.py", line 104, in __init__
    raise InvalidRequirement(
pkg_resources.extern.packaging.requirements.InvalidRequirement: Parse error at "'.'": Expected W:(abcd...)

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/bin/pipenv", line 8, in <module>
    sys.exit(cli())
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/vendor/click/core.py", line 1128, in __call__
    return self.main(*args, **kwargs)
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/vendor/click/core.py", line 1053, in main
    rv = self.invoke(ctx)
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/vendor/click/core.py", line 1659, in invoke
    return _process_result(sub_ctx.command.invoke(sub_ctx))
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/vendor/click/core.py", line 1395, in invoke
    return ctx.invoke(self.callback, **ctx.params)
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/vendor/click/core.py", line 754, in invoke
    return __callback(*args, **kwargs)
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/vendor/click/decorators.py", line 84, in new_func
    return ctx.invoke(f, obj, *args, **kwargs)
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/vendor/click/core.py", line 754, in invoke
    return __callback(*args, **kwargs)
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/cli/command.py", line 194, in install
    do_install(
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/core.py", line 2046, in do_install
    pkg_requirement = Requirement.from_line(pkg_line)
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/vendor/requirementslib/models/requirements.py", line 2674, in from_line
    parsed_line = Line(line)
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/vendor/requirementslib/models/requirements.py", line 171, in __init__
    self.parse()
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/vendor/requirementslib/models/requirements.py", line 1304, in parse
    self.parse_name()
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/vendor/requirementslib/models/requirements.py", line 1027, in parse_name
    name = self._parse_name_from_line()
  File "/home/dmr/.local/share/virtualenvs/hello-pipenv-xeMzRkcB/lib/python3.10/site-packages/pipenv/vendor/requirementslib/models/requirements.py", line 968, in _parse_name_from_line
    raise RequirementError(
pipenv.vendor.requirementslib.exceptions.RequirementError: Failed parsing requirement from '.'

```

This is unhelpful to say the least. From stackoverflow https://stackoverflow.com/questions/53378416/what-does-pipenv-install-e-do-and-how-to-use-it it looks like I'm meant to use setup.cfg or setup.py.

But then doesn't that mean I'm specifying my dependencies twice? https://pipenv.pypa.io/en/latest/advanced/#pipfile-vs-setuppy

I think this would have us
- specify general constraints in setup.py/cfg
- pin specific versions in Pipfile?

Not sure here. It doesn't feel like the workflow we want---there's now duplicate sources of truth. At least with pip-tools I could put everything in setup.cfg.

I tried copying in setup.cfg from the pip-tools example. I also had to copy in pyproject.toml. Something something PEP 518. Then I could install it:

```
dmr on titan in workspace/hello-pipenv is 📦 v0.0.1 via 🐍 v3.10.1 (hello-pipenv) 
2022-01-17 14:25:11 ✔  $ pipenv install --dev -e .
Installing -e ....
Adding hello to Pipfile's [dev-packages]...
✔ Installation Succeeded 
Pipfile.lock (1321e0) out of date, updating to (cc8a49)...
Locking [dev-packages] dependencies...
Building requirements...
Resolving dependencies...
✔ Success! 
Locking [packages] dependencies...
Building requirements...
Resolving dependencies...
✔ Success! 
Updated Pipfile.lock (cc8a49)!
Installing dependencies from Pipfile.lock (cc8a49)...
  🐍   ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 0/0 — 00:00:00

dmr on titan in workspace/hello-pipenv is 📦 v0.0.1 via 🐍 v3.10.1 (hello-pipenv) took 15s 
2022-01-17 14:25:42 ✔  $ less Pipfile
  [[source]]
  url = "https://pypi.python.org/simple"
  verify_ssl = true
  name = "pypi"
   
  [packages]
  requests = "*"
   
  [dev-packages]
  pytest = "*"
  black = "*"
  isort = "*"
  mypy = "*"
  pipenv = "*"
  hello = {editable = true, path = "."}
```

I was hoping for more explicit feedback about what exactly was installed. But it is nice that we can specify `pipenv install -e` in the spec/lock file itself.

Hashes are checked when installing or updating a package. Just changing a hash in the lock file and running "pipenv sync" didn't alert me to an error. I couldn't see a way to validate the lockfile against the virtualenv without actually changing the virtualenv. (I.e. no kind of validate or verify or --dry-run option.)

`pipenv graph` is pretty good!

```
$ pipenv graph
black==21.12b0
  - click [required: >=7.1.2, installed: 8.0.3]
  - mypy-extensions [required: >=0.4.3, installed: 0.4.3]
  - pathspec [required: >=0.9.0,<1, installed: 0.9.0]
  - platformdirs [required: >=2, installed: 2.4.1]
  - tomli [required: >=0.2.6,<2.0.0, installed: 1.2.3]
  - typing-extensions [required: >=3.10.0.0, installed: 4.0.1]
  - typing-extensions [required: !=3.10.0.1, installed: 4.0.1]
hello==0.0.1
  - requests [required: ~=2.0, installed: 2.27.1]
    - certifi [required: >=2017.4.17, installed: 2021.10.8]
    - charset-normalizer [required: ~=2.0.0, installed: 2.0.10]
    - idna [required: >=2.5,<4, installed: 3.3]
    - urllib3 [required: >=1.21.1,<1.27, installed: 1.26.8]
isort==5.10.1
mypy==0.931
  - mypy-extensions [required: >=0.4.3, installed: 0.4.3]
  - tomli [required: >=1.1.0, installed: 1.2.3]
  - typing-extensions [required: >=3.10, installed: 4.0.1]
pipenv==2022.1.8
  - certifi [required: Any, installed: 2021.10.8]
  - pip [required: >=18.0, installed: 21.3.1]
  - setuptools [required: >=36.2.1, installed: 60.5.0]
  - virtualenv [required: Any, installed: 20.13.0]
    - distlib [required: >=0.3.1,<1, installed: 0.3.4]
    - filelock [required: >=3.2,<4, installed: 3.4.2]
    - platformdirs [required: >=2,<3, installed: 2.4.1]
    - six [required: >=1.9.0,<2, installed: 1.16.0]
  - virtualenv-clone [required: >=0.2.5, installed: 0.5.7]
pytest==6.2.5
  - attrs [required: >=19.2.0, installed: 21.4.0]
  - iniconfig [required: Any, installed: 1.1.1]
  - packaging [required: Any, installed: 21.3]
    - pyparsing [required: >=2.0.2,!=3.0.5, installed: 3.0.6]
  - pluggy [required: >=0.12,<2.0, installed: 1.0.0]
  - py [required: >=1.8.2, installed: 1.11.0]
  - toml [required: Any, installed: 0.10.2]
```

No-op `pipenv update` took 12 sec. Contrast to 3-sec for a no-op `pip-compile setup.cfg --generate-hashes --extra lint,meta,test,dev --allow-unsafe -o requirements-dev.txt && pip-sync`.

## Outstanding questions

> Specify your target Python version in your Pipfile’s [requires] section. Ideally, you should only have one target Python version, as this is a deployment tool.

I think this means "when you're deploying an application, it should run on exactly one interpreter". I sympathise with this---but that's not our current model.


## TL;DR

Good:

- less config needed than pip-tools in places
- `pipenv check` for security (but this is mostly conveience; could pipe to `safety` with other tools)
- `pipenv graph` output better than pip-tools---though I would like the option to pipe to graphviz!
- Can specify editable install in the dev-requirements
- Hashes in the lock by default
- Lockfile includes markers like `python_version >= 3` etc. One lockfile for multiple platforms?
- Pycharm recognises Pipfiles


Neutral:

- `pipenv clean` --- not sure if this is needed--does `pipenv sync` do this?
- Don't understand the colour-coding of subcommands in `pipenv --help`.
- Separate to setuptools' `extras_requires`. Only have one optional `dev` for extras
- I seemed to have a `build` directory and I'm not sure where it came from. Nothing seemed to break when I removed it though.

Bad:

- Didn't find the documentation great
- Seemed to be slower than `pip-compile && pip-sync`, though it's possible pipenv is doing other things in the background that I don't realise
- Couldn't see many `--dry-run` options or "check what is needed but don't change stuff"
- Duplication with setuptools. Multiple sources of truth.
