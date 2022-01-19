# Poetry

direnv was a bit more involved. No out-of-the-box support; instead follow the direnv wiki https://github.com/direnv/direnv/wiki/Python#poetry

Then `echo layout poetry > .envrc`.

Does direnv work?

```
2022-01-18 14:40:35 ✔  $ cd -
/home/dmr/workspace/hello
direnv: loading ~/workspace/hello/.envrc

  PyProjectException

  [tool.poetry] section not found in /home/dmr/workspace/hello/pyproject.toml

  at ~/.local/lib/python3.10/site-packages/poetry/core/pyproject/toml.py:56 in poetry_config
      52│     def poetry_config(self):  # type: () -> Optional[TOMLDocument]
      53│         if self._poetry_config is None:
      54│             self._poetry_config = self.data.get("tool", {}).get("poetry")
      55│             if self._poetry_config is None:
    → 56│                 raise PyProjectException(
      57│                     "[tool.poetry] section not found in {}".format(self._file)
      58│                 )
      59│         return self._poetry_config
      60│ 
direnv: ([/usr/bin/direnv export bash]) is taking a while to execute. Use CTRL-C to give up.
^C
```

Nuke pyproject.toml and let poetry create one with `poetry init`. Interactive experience was quite pleasant.

```
 $ poetry add requests
Using version ^2.27.1 for requests

Updating dependencies
Resolving dependencies... (0.9s)

Writing lock file

Package operations: 5 installs, 0 updates, 0 removals

  • Installing certifi (2021.10.8)
  • Installing charset-normalizer (2.0.10)
  • Installing idna (3.3)
  • Installing urllib3 (1.26.8)
  • Installing requests (2.27.1)
```

Okay. Gave nice feedback.

```
2022-01-18 15:19:10 ✔  $ poetry add --dev black isort mypy
Using version ^21.12b0 for black
Using version ^5.10.1 for isort
Using version ^0.931 for mypy

Updating dependencies
Resolving dependencies... (1.7s)

Writing lock file

Package operations: 9 installs, 0 updates, 0 removals

  • Installing click (8.0.3)
  • Installing mypy-extensions (0.4.3)
  • Installing pathspec (0.9.0)
  • Installing platformdirs (2.4.1)
  • Installing tomli (1.2.3)
  • Installing typing-extensions (4.0.1)
  • Installing black (21.12b0)
  • Installing isort (5.10.1)
  • Installing mypy (0.931)
```

I tried adding an older version of mypy and got a useful error message. Though IMO I think the traceback isn't useful information. I suppose it would help getting decent a bug reports though.

Can I use an older mypy?

```
$ poetry add mypy==0.920

Updating dependencies
Resolving dependencies... (0.0s)

  SolverProblemError

  Because hello depends on both mypy (0.920) and mypy (^0.931), version solving failed.

  at ~/.local/lib/python3.10/site-packages/poetry/puzzle/solver.py:241 in _solve
      237│             packages = result.packages
      238│         except OverrideNeeded as e:
      239│             return self.solve_in_compatibility_mode(e.overrides, use_latest=use_latest)
      240│         except SolveFailure as e:
    → 241│             raise SolverProblemError(e)
      242│ 
      243│         results = dict(
      244│             depth_first_search(
      245│                 PackageNode(self._package, packages), aggregate_package_nodes
```

Instead, edit the pyproject toml and `poetry lock` then `poetry install`.

Lock file contains metadata including version markers, __even ones you're not using__:

```
  [[package]]
  name = "black"
  version = "21.12b0"
  description = "The uncompromising code formatter."
  category = "dev"
  optional = false
  python-versions = ">=3.6.2"
   
  [package.dependencies]
  click = ">=7.1.2"
  mypy-extensions = ">=0.4.3"
  pathspec = ">=0.9.0,<1"
  platformdirs = ">=2"
  tomli = ">=0.2.6,<2.0.0"
  typing-extensions = [
     {version = ">=3.10.0.0", markers = "python_version < \"3.10\""},
     {version = "!=3.10.0.1", markers = "python_version >= \"3.10\""},
  ]

```

This is potentially a killer feature over pip-tools.

Can see what the hashes mean:

```
keyring = [
      {file = "keyring-21.8.0-py3-none-any.whl", hash = "sha256:4be9cbaaaf83e61d6399f733d113ede7d1c73bc75cb6aeb64eee0f6ac39b30ea"},
      {file = "keyring-21.8.0.tar.gz", hash = "sha256:1746d3ac913d449a090caf11e9e4af00e26c3f7f7e81027872192b2398b98675"},
  ]
  lockfile = [
      {file = "lockfile-0.12.2-py2.py3-none-any.whl", hash = "sha256:6c3cb24f344923d30b2785d5ad75182c8ea7ac1b6171b08657258ec7429d50fa"},
      {file = "lockfile-0.12.2.tar.gz", hash = "sha256:6aed02de03cba24efabcd600b30540140634fc06cfa603822d508d5361e9f799"},
  ]
  msgpack = [
      {file = "msgpack-1.0.3-cp310-cp310-macosx_10_9_universal2.whl", hash = "sha256:96acc674bb9c9be63fa8b6dabc3248fdc575c4adc005c440ad02f87ca
  7edd079"},
      {file = "msgpack-1.0.3-cp310-cp310-macosx_10_9_x86_64.whl", hash = "sha256:2c3ca57c96c8e69c1a0d2926a6acf2d9a522b41dc4253a8945c4c6cd4981a
  4e3"},
```

Tree view nice:

```
 $ poetry show --tree
black 21.12b0 The uncompromising code formatter.
├── click >=7.1.2
│   └── colorama * 
├── mypy-extensions >=0.4.3
├── pathspec >=0.9.0,<1
├── platformdirs >=2
├── tomli >=0.2.6,<2.0.0
├── typing-extensions >=3.10.0.0
└── typing-extensions !=3.10.0.1
isort 5.10.1 A Python utility / library to sort Python imports.
mypy 0.920 Optional static typing for Python
├── mypy-extensions >=0.4.3,<0.5.0
├── tomli >=1.1.0,<3.0.0
└── typing-extensions >=3.7.4
requests 2.27.1 Python HTTP for Humans.
├── certifi >=2017.4.17
├── charset-normalizer >=2.0.0,<2.1.0
├── idna >=2.5,<4
└── urllib3 >=1.21.1,<1.27
```

Couldn't see a way to reverse this though, e.g. to show all of our reqasons for requiring `certifi`. I.e. I want to see the tree from a node upwards. Some hints in the lockfile. But only some. Maybe the idea is to show you the conflicting constraints and their source at `poetry install`-time?

Most recent pycharm has poetry support

Editable install of the project proper? Think this is done by default.

```
dmr on titan in hello on  poetry is 📦 v0.1.0 via 🐍 v3.10.1 (hello-3b1jrG99-py3.10) 
2022-01-18 16:53:32 ✔  $ pip show hello
Name: hello
Version: 0.1.0
Summary: Hello, world!
Home-page: 
Author: David Robertson
Author-email: davidr@element.io
License: WTFPL
Location: /home/dmr/.cache/pypoetry/virtualenvs/hello-3b1jrG99-py3.10/lib/python3.10/site-packages
Requires: requests
Required-by: 

dmr on titan in hello on  poetry is 📦 v0.1.0 via 🐍 v3.10.1 (hello-3b1jrG99-py3.10) 
2022-01-18 16:53:17 ✔  $ cat /home/dmr/.cache/pypoetry/virtualenvs/hello-3b1jrG99-py3.10/lib/python3.10/site-packages/hello.pth 
/home/dmr/workspace/hello
```

`poetry show --outdated` is nice

```
2022-01-18 16:54:51 ✔  $ poetry show --outdated
mypy  0.920 0.931 Optional static typing for Python
tomli 1.2.3 2.0.0 A lil' TOML parser


2022-01-18 16:55:30 ✔  $ poetry show --latest
black              21.12b0   21.12b0   The uncompromising code formatter.
certifi            2021.10.8 2021.10.8 Python package for providing Mozilla's CA Bundle.
charset-normalizer 2.0.10    2.0.10    The Real First Universal Charset Detector. Open, modern and actively maintained alternative to Char...
click              8.0.3     8.0.3     Composable command line interface toolkit
idna               3.3       3.3       Internationalized Domain Names in Applications (IDNA)
isort              5.10.1    5.10.1    A Python utility / library to sort Python imports.
mypy               0.920     0.931     Optional static typing for Python
mypy-extensions    0.4.3     0.4.3     Experimental type system extensions for programs checked with the mypy typechecker.
pathspec           0.9.0     0.9.0     Utility library for gitignore style pattern matching of file paths.
platformdirs       2.4.1     2.4.1     A small Python module for determining appropriate platform-specific dirs, e.g. a "user data dir".
requests           2.27.1    2.27.1    Python HTTP for Humans.
tomli              1.2.3     2.0.0     A lil' TOML parser
typing-extensions  4.0.1     4.0.1     Backported and Experimental Type Hints for Python 3.6+
urllib3            1.26.8    1.26.8    HTTP library with thread-safe connection pooling, file post, and more.
```

Generally felt snappy.


Hash checking? Certainly writes hashes. Is it going to check them? Seems to do so at the point of installation:

```
2022-01-18 17:12:33 ✔  $ poetry install 
Installing dependencies from lock file

Package operations: 1 install, 0 updates, 0 removals

  • Installing requests (2.27.1): Failed

  RuntimeError

  Retrieved digest for link requests-2.27.1.tar.gz(sha256:68d7c56fd5a8999887728ef304a6d12edc7be74f1cfa47714fc8b414525c9a61) not in poetry.lock metadata ['sha256:f22fa1e554c9ddfd16e6e41ac79aaaaaaaaaaaaaaaaaaaaaaaa054674760e72d', 'sha256:68d7c56fd5a8999887728ef304a6d12edc7be74faaaaaaaaaaaaaaaaaaaaaaaa']

  at ~/.local/lib/python3.10/site-packages/poetry/installation/chooser.py:113 in _get_links
      109│ 
      110│             selected_links.append(link)
      111│ 
      112│         if links and not selected_links:
    → 113│             raise RuntimeError(
      114│                 "Retrieved digest for link {}({}) not in poetry.lock metadata {}".format(
      115│                     link.filename, h, hashes
      116│                 )
      117│             )

```

Is that actually computing a hash or trusting the one it gets from the download?

I tried adding poetry as a dev dependency. Took a while---must be some dependency that doesn't provide its metadata. Fast afterwards though. 

Jesus, it pulls in a lot.

```
dmr on titan in hello on  poetry is 📦 v0.1.0 via 🐍 v3.10.1 (hello-3b1jrG99-py3.10) 
2022-01-18 17:13:34 ✗ 1 ERROR  $ poetry add --dev poetry
Using version ^1.1.12 for poetry

Updating dependencies
Resolving dependencies... (10.5s)

  PermissionError

  [Errno 13] Permission denied: '/home/dmr/workspace/hello/poetry.lock'

  at ~/.local/lib/python3.10/site-packages/tomlkit/toml_file.py:18 in write
      14│         with open(self._path, encoding="utf-8", newline="") as f:
      15│             return loads(f.read())
      16│ 
      17│     def write(self, data: TOMLDocument) -> None:
    → 18│         with open(self._path, "w", encoding="utf-8", newline="") as f:
      19│             f.write(data.as_string())
      20│ 

dmr on titan in hello on  poetry is 📦 v0.1.0 via 🐍 v3.10.1 (hello-3b1jrG99-py3.10) took 11s 
2022-01-18 17:13:48 ✗ 1 ERROR  $ chmod 644 poetry.lock 

dmr on titan in hello on  poetry is 📦 v0.1.0 via 🐍 v3.10.1 (hello-3b1jrG99-py3.10) 
2022-01-18 17:14:01 ✔  $ poetry add --dev poetry
Using version ^1.1.12 for poetry

Updating dependencies
Resolving dependencies... (0.5s)

Writing lock file

Package operations: 32 installs, 0 updates, 0 removals

  • Installing pycparser (2.21)
  • Installing cffi (1.15.0)
  • Installing crashtest (0.3.1)
  • Installing cryptography (36.0.1)
  • Installing jeepney (0.7.1)
  • Installing pastel (0.2.1)
  • Installing pylev (1.4.0)
  • Installing clikit (0.6.2)
  • Installing distlib (0.3.4)
  • Installing filelock (3.4.2)
  • Installing lockfile (0.12.2)
  • Installing msgpack (1.0.3)
  • Installing ptyprocess (0.7.0)
  • Installing pyparsing (3.0.6)
  • Installing requests (2.27.1)
  • Installing secretstorage (3.3.1)
  • Installing six (1.16.0)
  • Installing webencodings (0.5.1)
  • Installing cachecontrol (0.12.10)
  • Installing cachy (0.3.0)
  • Installing cleo (0.8.1)
  • Installing html5lib (1.1)
  • Installing keyring (21.8.0)
  • Installing packaging (20.9)
  • Installing pexpect (4.8.0)
  • Installing pkginfo (1.8.2)
  • Installing poetry-core (1.0.7)
  • Installing requests-toolbelt (0.9.1)
  • Installing shellingham (1.4.0)
  • Installing tomlkit (0.8.0)
  • Installing virtualenv (20.13.0)
  • Installing poetry (1.1.12)
```





Build says 
> only pure python wheels are supported.

dunno if that's a problem for us. But it does let you say `-f  sdist`???

We currently build a wheel and a tar.gz. Fun and games down the line if we want to make use of rust?
But https://github.com/pyo3/maturin#mixed-rustpython-projects might possibly be useful.

## TL;DR

Good:
- feels snappy, decent error messages and output.
- `--dry-run` everywhere.
- pycharm integration
- embeds more data in the lock, in particular different environment markers?
- will use an existing venv if one exists

Neutral:
- has its own pinning syntax, foo^=1.2.3.
  - (Anything semver compatible with rhs. I think this means `[1.2.3, 2)`. 
  - contrast to foo~=1.2.3, which means `[1.2.3, 1.3)
- afaics we're not obliged to use it for testing (`poetry install; poetry run trial`.)
- unsure about how builds work.
- can export a requirements.txt if we don't like it

Bad:
- bigger tool
- more work to use (setup.* -> pyproject.toml) and maybe a little off the beaten track
  - feels like there's more community momentum though.

# Python versions

Let's see how environment markers get handled. I'll start with python versions, for which poetry has dedicated syntax.

I don't remember marking the project as `^3.10`. Let's relax that to `^3.7`. I'll pull in a different version of `pillow` on different python versions.

```toml
[tool.poetry.dependencies]
python = "^3.7"
requests = "^2.27.1"
pillow = [
  { version = "^9.0.0", python = "^3.9" },
  { version = "~7.0", python = "~3.7,~3.8" },
]
```

Probably best to point at the [diff](https://github.com/DMRobertson/test-packaging/commit/9877b812786f4533dcde2d658c03ba0d8b18c818) here. 

I think that:
- the lockfile will only contain stuff for the Python version supported at the top level
- python versions and environment markers are probably best kept mutually exclusive (see [here](https://github.com/python-poetry/poetry/issues/5066)).

Doing this and locking spews out a big diff. Note that
- I ran this on 3.10, but we now have a transitive dependency on `typed-ast` described in the lock, for <3.8 only
- pillow appears twice in the lock file's list of packages
- hashes for pillow 7 and pillow 9 wheels etc appear in the metadata.

installing under a 3.7 env (`poetry env use 3.7`) installs pillow 7.
installing under a 3.9 env (`poetry env use 3.9`) installs pillow 9. Confusingly, `poetry show --tree` shows both---I guess because they'll have different dependencies.

What about an environment marker? Let's try installing something only under pypy.

```
lxml = { version = "~4.2.0", markers = "platform_python_implementation == 'PyPy'" }
```

See [diff](https://github.com/DMRobertson/test-packaging/commit/a227b6e7a7d18ba946d54ca3c9683387c0ad890d) for the lockfile change. No changes when I install though:

```
 $ poetry install
Installing dependencies from lock file

No dependencies to install or update

Installing the current project: hello (0.1.0)
```

I was able to create a pypy environment with `poetry env use pypy3`. But this clashed with an existing 3.7 virtualenv it was already managing. This is https://github.com/python-poetry/poetry/issues/2089. I wasn't able to install `typed-ast` on pypy because compiling some kind of C extension failed. Let's try simplifying the toml file.

```toml
[tool.poetry]
name = "hello"
version = "0.1.0"
description = "Hello, world!"
authors = ["David Robertson <davidr@element.io>"]
license = "WTFPL"
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.7"
requests = "^2.27.1"
lxml = { version = "~4.2.0", markers = "platform_python_implementation == 'PyPy'" }

[build-system]
requires = ["poetry-core>=1.0.0"]
build-backend = "poetry.core.masonry.api"
```

When I installed in the pypy env, I got lxml. On Cpython env, I didn't. Nice.


# Extras

Specify as follows:

```
[tool.poetry.dependencies]
# ...
aiohttp = { version = "^3.8", optional = true }
aio_ping = { version = "^0.1.3", optional = true }

[tool.poetry.extras]
aio = ["aiohttp", "aio_ping"]
```

Lockfile adds in a bunch of guff. `poetry install` does nothing. `poetry install -E aio` does. I think this is working as intended.
