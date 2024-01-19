### Build Docs releasenotes/config.yaml is modified (Tests #11: Commit 2a3f59a pushed by Naohnakazawa)
### tox -elint
```
2024-01-12T15:50:36.9928744Z lint: commands[0]> black --check qiskit_experiments test tools setup.py
2024-01-12T15:50:40.1442083Z All done! ✨ 🍰 ✨
2024-01-12T15:50:40.1442905Z 262 files would be left unchanged.
2024-01-12T15:50:40.1612763Z lint: commands[1]> pylint -rn --rcfile=/home/runner/work/qiskit-experiments/qiskit-experiments/.pylintrc qiskit_experiments/ test/ tools/
2024-01-12T15:51:52.7652991Z 
2024-01-12T15:51:52.7653922Z ------------------------------------
2024-01-12T15:51:52.7656448Z Your code has been rated at 10.00/10
2024-01-12T15:51:52.7657106Z 
2024-01-12T15:52:00.5773444Z lint: commands[2]> python /home/runner/work/qiskit-experiments/qiskit-experiments/tools/verify_headers.py
2024-01-12T15:52:00.6508284Z .pkg: _exit> python /opt/hostedtoolcache/Python/3.8.18/x64/lib/python3.8/site-packages/pyproject_api/_backend.py True setuptools.build_meta
2024-01-12T15:52:00.6713763Z   lint: OK (134.06=setup[50.40]+cmd[3.17,80.42,0.07] seconds)
2024-01-12T15:52:00.6714656Z   congratulations :) (134.52 seconds)
2024-01-12T15:52:00.7676024Z Post job cleanup.
2024-01-12T15:52:00.8813478Z Cache hit occurred on the primary key Linux-pip-lint-215038029a296288dcba08135f26723fa061c10a882b7a6c78afc4b5eead9912, not saving cache.
2024-01-12T15:52:00.8929495Z Post job cleanup.
2024-01-12T15:52:01.0376064Z Post job cleanup.
2024-01-12T15:52:01.1207435Z [command]/usr/bin/git version
2024-01-12T15:52:01.1250873Z git version 2.43.0
2024-01-12T15:52:01.1289381Z Temporarily overriding HOME='/home/runner/work/_temp/8ca501fe-485a-4005-9cc0-44a22c010b3b' before making global git config changes
2024-01-12T15:52:01.1291077Z Adding repository directory to the temporary git global config as a safe directory
2024-01-12T15:52:01.1295602Z [command]/usr/bin/git config --global --add safe.directory /home/runner/work/qiskit-experiments/qiskit-experiments
2024-01-12T15:52:01.1336629Z [command]/usr/bin/git config --local --name-only --get-regexp core\.sshCommand
2024-01-12T15:52:01.1375023Z [command]/usr/bin/git submodule foreach --recursive sh -c "git config --local --name-only --get-regexp 'core\.sshCommand' && git config --local --unset-all 'core.sshCommand' || :"
2024-01-12T15:52:01.1629716Z [command]/usr/bin/git config --local --name-only --get-regexp http\.https\:\/\/github\.com\/\.extraheader
2024-01-12T15:52:01.1659011Z http.https://github.com/.extraheader
2024-01-12T15:52:01.1669739Z [command]/usr/bin/git config --local --unset-all http.https://github.com/.extraheader
2024-01-12T15:52:01.1707655Z [command]/usr/bin/git submodule foreach --recursive sh -c "git config --local --name-only --get-regexp 'http\.https\:\/\/github\.com\/\.extraheader' && git config --local --unset-all 'http.https://github.com/.extraheader' || :"
2024-01-12T15:52:01.2199921Z Cleaning up orphan processes
```

### Build Docs releasenotes/config.yaml is modified (Tests #11: Commit 2a3f59a pushed by Naohnakazawa)
### tox -edocs-parallel 
```
2024-01-15T14:30:27.7427008Z scanning /home/runner/work/qiskit-experiments/qiskit-experiments/releasenotes/notes for current branch release notes
2024-01-15T14:30:27.7496818Z 
2024-01-15T14:30:27.7497224Z Traceback (most recent call last):
2024-01-15T14:30:27.7506781Z   File "/home/runner/work/qiskit-experiments/qiskit-experiments/.tox/docs-parallel/lib/python3.8/site-packages/sphinx/cmd/build.py", line 285, in build_main
2024-01-15T14:30:27.7508057Z     app.build(args.force_all, args.filenames)
2024-01-15T14:30:27.7509284Z   File "/home/runner/work/qiskit-experiments/qiskit-experiments/.tox/docs-parallel/lib/python3.8/site-packages/sphinx/application.py", line 353, in build
2024-01-15T14:30:27.7510238Z     self.builder.build_update()
2024-01-15T14:30:27.7511497Z   File "/home/runner/work/qiskit-experiments/qiskit-experiments/.tox/docs-parallel/lib/python3.8/site-packages/sphinx/builders/__init__.py", line 311, in build_update
2024-01-15T14:30:27.7512513Z     self.build(to_build,
2024-01-15T14:30:27.7513659Z   File "/home/runner/work/qiskit-experiments/qiskit-experiments/.tox/docs-parallel/lib/python3.8/site-packages/sphinx/builders/__init__.py", line 328, in build
2024-01-15T14:30:27.7514645Z     updated_docnames = set(self.read())
2024-01-15T14:30:27.7515713Z   File "/home/runner/work/qiskit-experiments/qiskit-experiments/.tox/docs-parallel/lib/python3.8/site-packages/sphinx/builders/__init__.py", line 433, in read
2024-01-15T14:30:27.7516868Z     self._read_parallel(docnames, nproc=self.app.parallel)
2024-01-15T14:30:27.7518394Z   File "/home/runner/work/qiskit-experiments/qiskit-experiments/.tox/docs-parallel/lib/python3.8/site-packages/sphinx/builders/__init__.py", line 489, in _read_parallel
2024-01-15T14:30:27.7519461Z     tasks.join()
2024-01-15T14:30:27.7520595Z   File "/home/runner/work/qiskit-experiments/qiskit-experiments/.tox/docs-parallel/lib/python3.8/site-packages/sphinx/util/parallel.py", line 99, in join
2024-01-15T14:30:27.7521771Z     if not self._join_one():
2024-01-15T14:30:27.7522872Z   File "/home/runner/work/qiskit-experiments/qiskit-experiments/.tox/docs-parallel/lib/python3.8/site-packages/sphinx/util/parallel.py", line 119, in _join_one
2024-01-15T14:30:27.7524255Z     raise SphinxParallelError(*result)
2024-01-15T14:30:27.7525058Z sphinx.errors.SphinxParallelError: ValueError: earliest-version set to unknown revision '0.9.0'
2024-01-15T14:30:27.7525608Z 
2024-01-15T14:30:27.7525754Z Sphinx parallel build error:
2024-01-15T14:30:27.7526377Z ValueError: earliest-version set to unknown revision '0.9.0'
2024-01-15T14:30:28.4410204Z docs-parallel: exit 2 (711.85 seconds) /home/runner/work/qiskit-experiments/qiskit-experiments> sphinx-build -j auto -T -W --keep-going -b html docs/ docs/_build/html pid=2331
2024-01-15T14:30:28.4418690Z .pkg: _exit> python /opt/hostedtoolcache/Python/3.8.18/x64/lib/python3.8/site-packages/pyproject_api/_backend.py True setuptools.build_meta
2024-01-15T14:30:28.4624688Z   docs-parallel: FAIL code 2 (772.15=setup[60.30]+cmd[711.85] seconds)
2024-01-15T14:30:28.4625591Z   evaluation failed :( (772.61 seconds)
2024-01-15T14:30:28.4885426Z ##[error]Process completed with exit code 2.
2024-01-15T14:30:28.4988105Z Post job cleanup.
2024-01-15T14:30:28.5820577Z [command]/usr/bin/git version
2024-01-15T14:30:28.5867151Z git version 2.43.0
2024-01-15T14:30:28.5908258Z Temporarily overriding HOME='/home/runner/work/_temp/ca57d6c5-e042-48b7-aeb5-6b3b1213b025' before making global git config changes
2024-01-15T14:30:28.5910744Z Adding repository directory to the temporary git global config as a safe directory
2024-01-15T14:30:28.5915776Z [command]/usr/bin/git config --global --add safe.directory /home/runner/work/qiskit-experiments/qiskit-experiments
2024-01-15T14:30:28.5958072Z [command]/usr/bin/git config --local --name-only --get-regexp core\.sshCommand
2024-01-15T14:30:28.5999863Z [command]/usr/bin/git submodule foreach --recursive sh -c "git config --local --name-only --get-regexp 'core\.sshCommand' && git config --local --unset-all 'core.sshCommand' || :"
2024-01-15T14:30:28.6253936Z [command]/usr/bin/git config --local --name-only --get-regexp http\.https\:\/\/github\.com\/\.extraheader
2024-01-15T14:30:28.6283339Z http.https://github.com/.extraheader
2024-01-15T14:30:28.6295063Z [command]/usr/bin/git config --local --unset-all http.https://github.com/.extraheader
2024-01-15T14:30:28.6335307Z [command]/usr/bin/git submodule foreach --recursive sh -c "git config --local --name-only --get-regexp 'http\.https\:\/\/github\.com\/\.extraheader' && git config --local --unset-all 'http.https://github.com/.extraheader' || :"
2024-01-15T14:30:28.6813652Z Cleaning up orphan processes
```



### tox (local, 20240116)
### tox -elint
```
(experiments-dev) nakazawa@nakazawa-macbook qiskit-experiments % tox -elint
.pkg: _optional_hooks> python /Users/nakazawa/.local/lib/python3.10/site-packages/pyproject_api/_backend.py True setuptools.build_meta
.pkg: get_requires_for_build_sdist> python /Users/nakazawa/.local/lib/python3.10/site-packages/pyproject_api/_backend.py True setuptools.build_meta
.pkg: get_requires_for_build_editable> python /Users/nakazawa/.local/lib/python3.10/site-packages/pyproject_api/_backend.py True setuptools.build_meta
.pkg: build_editable> python /Users/nakazawa/.local/lib/python3.10/site-packages/pyproject_api/_backend.py True setuptools.build_meta
lint: install_package> pip install -c/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/constraints.txt -U --force-reinstall --no-deps /Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/.tmp/package/399/qiskit_experiments-0.6.0-0.editable-py3-none-any.whl
lint: commands[0]> black --check qiskit_experiments test tools setup.py
All done! ✨ 🍰 ✨
262 files would be left unchanged.
lint: commands[1]> pylint -rn --rcfile=/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.pylintrc qiskit_experiments/ test/ tools/

--------------------------------------------------------------------
Your code has been rated at 10.00/10 (previous run: 10.00/10, +0.00)

lint: commands[2]> python /Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/tools/verify_headers.py
multiprocessing.pool.RemoteTraceback: 
"""
Traceback (most recent call last):
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/lib/python3.10/multiprocessing/pool.py", line 125, in worker
    result = (True, func(*args, **kwds))
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/lib/python3.10/multiprocessing/pool.py", line 48, in mapstar
    return list(map(*args))
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/tools/verify_headers.py", line 63, in validate_header
    lines = fd.readlines()
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/lib/python3.10/codecs.py", line 322, in decode
    (result, consumed) = self._buffer_decode(data, self.errors, final)
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xb3 in position 36: invalid start byte
"""

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/tools/verify_headers.py", line 105, in <module>
    main()
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/tools/verify_headers.py", line 94, in main
    res = pool.map(validate_header, files)
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/lib/python3.10/multiprocessing/pool.py", line 367, in map
    return self._map_async(func, iterable, mapstar, chunksize).get()
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/lib/python3.10/multiprocessing/pool.py", line 774, in get
    raise self._value
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xb3 in position 36: invalid start byte
lint: exit 1 (18.79 seconds) /Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments> python /Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/tools/verify_headers.py pid=1694
.pkg: _exit> python /Users/nakazawa/.local/lib/python3.10/site-packages/pyproject_api/_backend.py True setuptools.build_meta
  lint: FAIL code 1 (115.13=setup[7.80]+cmd[1.27,87.27,18.79] seconds)
  evaluation failed :( (116.08 seconds)
```
### an alternative code suggested in Stack Overflow
```
chage the line 62 in File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/tools/verify_headers.py"
(befre): with open(file_path, encoding="utf8") as fd:
(after): with open(file_path, encoding="utf16") as fd:
         with open(file_path, encoding="utf-16-le") as fd:
         with open(file_path, encoding="utf-16-be") as fd:
         with open(file_path, 'rb') as fd:

Unfortunately, these do not work!　Do not change this line.
```


### tox (local, 20240116)
### tox -e docs
```
Traceback (most recent call last):
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/sphinx/cmd/build.py", line 285, in build_main
    app.build(args.force_all, args.filenames)
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/sphinx/application.py", line 353, in build
    self.builder.build_update()
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/sphinx/builders/__init__.py", line 311, in build_update
    self.build(to_build,
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/sphinx/builders/__init__.py", line 328, in build
    updated_docnames = set(self.read())
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/sphinx/builders/__init__.py", line 435, in read
    self._read_serial(docnames)
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/sphinx/builders/__init__.py", line 456, in _read_serial
    self.read_doc(docname)
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/sphinx/builders/__init__.py", line 512, in read_doc
    publisher.publish()
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/docutils/core.py", line 224, in publish
    self.document = self.reader.read(self.source, self.parser,
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/sphinx/io.py", line 108, in read
    self.parse()
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/docutils/readers/__init__.py", line 76, in parse
    self.parser.parse(self.input, document)
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/sphinx/parsers.py", line 80, in parse
    self.statemachine.run(inputlines, document, inliner=self.inliner)
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/docutils/parsers/rst/states.py", line 169, in run
    results = StateMachineWS.run(self, input_lines, input_offset,
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/docutils/statemachine.py", line 233, in run
    context, next_state, result = self.check_line(
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/docutils/statemachine.py", line 445, in check_line
    return method(match, context, next_state)
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/docutils/parsers/rst/states.py", line 2355, in explicit_markup
    nodelist, blank_finish = self.explicit_construct(match)
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/docutils/parsers/rst/states.py", line 2367, in explicit_construct
    return method(self, expmatch)
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/docutils/parsers/rst/states.py", line 2104, in directive
    return self.run_directive(
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/docutils/parsers/rst/states.py", line 2154, in run_directive
    result = directive_instance.run()
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/reno/sphinxext.py", line 114, in run
    with loader.Loader(conf) as ldr:
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/reno/loader.py", line 62, in __init__
    self._load_data()
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/reno/loader.py", line 90, in _load_data
    self._scanner_output = self._scanner.get_notes_by_version()
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/reno/scanner.py", line 1003, in get_notes_by_version
    raise ValueError(
ValueError: earliest-version set to unknown revision '0.9.0'

例外が発生しました
  File "/Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments/.tox/docs/lib/python3.10/site-packages/reno/scanner.py", line 1003, in get_notes_by_version
    raise ValueError(
ValueError: earliest-version set to unknown revision '0.9.0'
完全なトレースバックを/var/folders/l3/kffgnfb96z110f1fb3bvlny00000gn/T/sphinx-err-vacwrs1k.logに保存しました。問題を開発者に報告するときに添付してください。
次期バージョンでのエラーメッセージ改善のために、ユーザーエラーの場合にも報告してください。
バグ報告はこちらにお願いします <https://github.com/sphinx-doc/sphinx/issues> 。ご協力ありがとうございます！
docs: exit 2 (797.12 seconds) /Users/nakazawa/anaconda3/envs/experiments-dev/qiskit-experiments> sphinx-build -T -W --keep-going -b html docs/ docs/_build/html pid=2203
.pkg: _exit> python /Users/nakazawa/.local/lib/python3.10/site-packages/pyproject_api/_backend.py True setuptools.build_meta
  docs: FAIL code 2 (819.89=setup[22.77]+cmd[797.12] seconds)
  evaluation failed :( (820.87 seconds)
```


### tox (local-imac-python3.8, 20240119 )
### tox -elint
```
(experiments-dev8) nakazawanaohito@nakazawa-iMac qiskit-experiments % tox -elint
.pkg: _optional_hooks> python /Users/nakazawanaohito/.local/lib/python3.8/site-packages/pyproject_api/_backend.py True setuptools.build_meta
.pkg: get_requires_for_build_sdist> python /Users/nakazawanaohito/.local/lib/python3.8/site-packages/pyproject_api/_backend.py True setuptools.build_meta
.pkg: get_requires_for_build_editable> python /Users/nakazawanaohito/.local/lib/python3.8/site-packages/pyproject_api/_backend.py True setuptools.build_meta
.pkg: build_editable> python /Users/nakazawanaohito/.local/lib/python3.8/site-packages/pyproject_api/_backend.py True setuptools.build_meta
lint: install_package> pip install -c/Users/nakazawanaohito/anaconda3/envs/experiments-dev8/qiskit-experiments/constraints.txt -U --force-reinstall --no-deps /Users/nakazawanaohito/anaconda3/envs/experiments-dev8/qiskit-experiments/.tox/.tmp/package/14/qiskit_experiments-0.6.0-0.editable-py3-none-any.whl
lint: commands[0]> black --check qiskit_experiments test tools setup.py
All done! ✨ 🍰 ✨
263 files would be left unchanged.
lint: commands[1]> pylint -rn --rcfile=/Users/nakazawanaohito/anaconda3/envs/experiments-dev8/qiskit-experiments/.pylintrc qiskit_experiments/ test/ tools/

--------------------------------------------------------------------
Your code has been rated at 10.00/10 (previous run: 10.00/10, +0.00)

lint: commands[2]> python /Users/nakazawanaohito/anaconda3/envs/experiments-dev8/qiskit-experiments/tools/verify_headers.py
.pkg: _exit> python /Users/nakazawanaohito/.local/lib/python3.8/site-packages/pyproject_api/_backend.py True setuptools.build_meta
  lint: OK (125.95=setup[10.97]+cmd[1.34,112.38,1.26] seconds)
  congratulations :) (131.58 seconds)
```







