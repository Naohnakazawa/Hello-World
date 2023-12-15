# Hello-World

This is **Naohito Nakazawa's** first repository on github. *I am*

## *a physicist* 
### a part-time lecturer in Waseda University

a begnner of github and also, 
a begnner of python

This is a <sub>subscript</sub> text.

This is a <sup>superscript</sup> test.

Today is a **holiday**.
> Today is a **holiday**.

Some basic Git commands are:
```
git status
git add
git diff 
git commit
git log 
git checkout # dangerous!
git restore  # dangerous!
```

An interesting paper is [here](https://arxiv.org/abs/2308.02321).




### LocalReadoutError
```
# backend 
from qiskit_aer import AerSimulator
from qiskit.providers.fake_provider import FakePerth

backend = AerSimulator.from_backend(FakePerth())

# mwc
from qiskit_experiments.library import LocalReadoutError

qubits = list(range(4))
exp = LocalReadoutError(physical_qubits=qubits, backend=backend)
exp.analysis.set_options(plot=True)
exp.set_run_options(shots=10000)

result = exp.run()
mitigator = result.analysis_results(0).value

result.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/88e75bfd-6eab-4f18-b6b4-dd67df46036b)






### CorrelatedReadoutError
```
# backend ibm_kyoto
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_kyoto")

#mwc
from qiskit_experiments.library import CorrelatedReadoutError

qubits = [10,11,12]
num_qubits = len(qubits)
exp = CorrelatedReadoutError(physical_qubits=qubits, backend=backend)
exp.set_run_options(shots=10000)
exp.analysis.set_options(plot=True)

for c in exp.circuits():
    print(c)
    
step1=False # run carefully if your device is a real one!
if step1==True:
    
    exp_data = exp.run()
    mitigator = exp_data.analysis_results(0).value
    result=exp_data.analysis_results()    
    for _ in result:
        print(_)
        
    exp_data.figure(0)
    
else:
    pass

# retrieve your jobs
from qiskit_experiments.framework import ExperimentData 

jobs_ids=["cnqvks7gsbh0008svjf0"] # list of job IDs for the experiment
exp_data = ExperimentData(experiment=exp)
exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp.analysis.run(exp_data)
exp_data.block_for_results()
result=exp_data.analysis_results()

exp_data.figure(0)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/0663868e-8bf9-49b1-b9af-b6296316ad9d)






### Issue#1238(also #1221)

### reno

features:
  - |
    List new features here, or remove this section.  All of the list items in
    this section are combined when the release notes are rendered, so the text
    needs to be worded so that it does not depend on any information only
    available in another section, such as the prelude. This may mean repeating
    some details.


### Pull request checklist (for qiskit)

1. The code follows the code style of the project and successfully
   passes the tests. For convenience, you can execute `tox` locally,
   which will run these checks and report any issues.
   If your code fails the local style checks (specifically the black
   code formatting check) you can use `tox -eblack` to automatically
   fix update the code formatting.

tox -epy310                      : well-done

tox -epy38                       : well-done

tox -elint & tox -eblack         : well-done

tox -e docs                      : Tere are some issues.

2. The documentation has been updated accordingly. In particular, if a
   function or class has been modified during the PR, please update the
   *docstring* accordingly.

   If your pull request is adding a new class, function, or module that is
   intended to be user facing ensure that you've also added those to a
   documentation `autosummary` index to include it in the api documentation.
   For more details you can refer to:

   https://qiskit.org/documentation/contributing_to_qiskit.html#documentation-structure


3. If it makes sense for your change that you have added new tests that
   cover the changes.


4. Ensure that if your change has an end user facing impact (new feature,
   deprecation, removal etc) that you have added a reno release note for that
   change and that the PR is tagged for the changelog.


### Pull request checklist (for qiskit-experiments)

When submitting a pull request for review, please ensure that:

1. The code follows the code style of the project and successfully passes the tests.

tox -epy310                      : well-done

tox -elint & tox -eblack         : well-done
.
tox -e docs                      : Tere are some issues.

2. The API documentation has been updated accordingly.

No documentation has been created.

3. You have updated the relevant documentation or written new docs. In case the PR needs
   to be merged without delay (e.g. for a high priority fix), open an issue for updating
   or adding the documentation later.

No documentation has been created.

4. You've added tests that cover the changes you've made, if relevant.

5. If your change has an end user facing impact (new feature, deprecation, removal,
   etc.), you've added or updated a reno release note for that change and tagged the PR
   for the changelog.

6. If your code requires a change to dependencies, you've updated the corresponding
   requirements file: `requirements.txt` contain core dependencies,
   `requirements-extras.txt` for dependencies for optional features, and `requirements-dev.txt`
   for dependencies required for running tests and building documentation.


### an example of workflow ( from github.com/Qiskit-Extensions/qiskit-experiments )
```
name: Cron-staging
on:
  schedule:
    - cron: "0 4 * * *"
  workflow_dispatch:

jobs:
  qiskit-main-tests:
    name: tests-python${{ matrix.python-version }}-${{ matrix.os }}
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        python-version: [3.8, "3.11"]
        os: ["ubuntu-latest", "macOS-latest", "windows-latest"]
    steps:
      - name: Print Concurrency Group
        env:
          CONCURRENCY_GROUP: ${{ github.repository }}-${{ github.ref }}-${{ github.head_ref }}
        run: |
          echo -e "\033[31;1;4mConcurrency Group\033[0m"
          echo -e "$CONCURRENCY_GROUP\n"
        shell: bash
      - uses: actions/checkout@v3
      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v4
        with:
          python-version: ${{ matrix.python-version }}
      - name: Pip cache
        uses: actions/cache@v3
        with:
          path: ~/.cache/pip
          key: ${{ runner.os }}-${{ matrix.python-version }}-pip-tests-${{ hashFiles('setup.py','requirements.txt','requirements-extras.txt','requirements-dev.txt','constraints.txt') }}
          restore-keys: |
            ${{ runner.os }}-${{ matrix.python-version }}-pip-tests-
            ${{ runner.os }}-${{ matrix.python-version }}-pip-
            ${{ runner.os }}-${{ matrix.python-version }}
      - name: Install Deps
        run: python -m pip install -U tox setuptools virtualenv wheel
      - name: Install and Run Tests (Windows and Linux)
        run: tox -e qiskit-main
        if: runner.os != 'macOS'
      - name: Install and Run Tests (Macs only)
        run: tox -e qiskit-main
        if: runner.os == 'macOS'
        env:
          TEST_TIMEOUT: 120
          OMP_NUM_THREADS: 1
  docs:
    name: docs
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      - name: Set up Python 3.11
        uses: actions/setup-python@v4
        with:
          python-version: 3.11
      - name: Pip cache
        uses: actions/cache@v3
        with:
          path: ~/.cache/pip
          key: ${{ runner.os }}-pip-docs-${{ hashFiles('setup.py','requirements.txt','requirements-extras.txt','requirements-dev.txt','constraints.txt') }}
      - name: Install Deps
        run: |
          python -m pip install -U tox
          sudo apt-get install -y pandoc graphviz
      - name: Build Docs
        run: tox -edocs-qiskit-main
      - name: Compress Artifacts
        run: |
          mkdir artifacts
          tar -Jcvf html_docs.tar.xz docs/_build/html
          mv html_docs.tar.xz artifacts/.
      - uses: actions/upload-artifact@v3
        with:
          name: html_docs
          path: artifacts
```



