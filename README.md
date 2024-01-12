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
prelude: >
    Replace this text with content to appear at the top of the section for this
    release. All of the prelude content is merged together and then rendered
    separately from the items listed in other parts of the file, so the text
    needs to be worded so that both the prelude and the other items make sense
    when read independently. This may mean repeating some details. Not every
    release note requires a prelude. Usually only notes describing major
    features or adding release theme details should have a prelude.
features:
  - |
    List new features here, or remove this section.  All of the list items in
    this section are combined when the release notes are rendered, so the text
    needs to be worded so that it does not depend on any information only
    available in another section, such as the prelude. This may mean repeating
    some details.
issues:
  - |
    List known issues here, or remove this section.  All of the list items in
    this section are combined when the release notes are rendered, so the text
    needs to be worded so that it does not depend on any information only
    available in another section, such as the prelude. This may mean repeating
    some details.
upgrade:
  - |
    List upgrade notes here, or remove this section.  All of the list items in
    this section are combined when the release notes are rendered, so the text
    needs to be worded so that it does not depend on any information only
    available in another section, such as the prelude. This may mean repeating
    some details.
deprecations:
  - |
    List deprecations notes here, or remove this section.  All of the list
    items in this section are combined when the release notes are rendered, so
    the text needs to be worded so that it does not depend on any information
    only available in another section, such as the prelude. This may mean
    repeating some details.
critical:
  - |
    Add critical notes here, or remove this section.  All of the list items in
    this section are combined when the release notes are rendered, so the text
    needs to be worded so that it does not depend on any information only
    available in another section, such as the prelude. This may mean repeating
    some details.
security:
  - |
    Add security notes here, or remove this section.  All of the list items in
    this section are combined when the release notes are rendered, so the text
    needs to be worded so that it does not depend on any information only
    available in another section, such as the prelude. This may mean repeating
    some details.
fixes:
  - |
    Add normal bug fixes here, or remove this section.  All of the list items
    in this section are combined when the release notes are rendered, so the
    text needs to be worded so that it does not depend on any information only
    available in another section, such as the prelude. This may mean repeating
    some details.
developer:
  - |
    Add upgrade of protected-level API (e.g. hooks). All of the list items
    in this section are combined when the release notes are rendered, so the
    text needs to be worded so that it does not depend on any information only
    available in another section, such as the prelude. This may mean repeating
    some details.
other:
  - |
    Add other notes here, or remove this section.  All of the list items in
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

tox -e docs                      : Tere are some issues. This must be fiexd.

2. The API documentation has been updated accordingly.

No documentation has been created. This must be fixed.

3. You have updated the relevant documentation or written new docs. In case the PR needs
   to be merged without delay (e.g. for a high priority fix), open an issue for updating
   or adding the documentation later.

No documentation has been created. This must be fixed.

4. You've added tests that cover the changes you've made, if relevant.

I've not added any tests. This may be no problem.

5. If your change has an end user facing impact (new feature, deprecation, removal,
   etc.), you've added or updated a reno release note for that change and tagged the PR
   for the changelog.

I've not added any reno release notes. This may be no problem

6. If your code requires a change to dependencies, you've updated the corresponding
   requirements file: `requirements.txt` contain core dependencies,
   `requirements-extras.txt` for dependencies for optional features, and `requirements-dev.txt`
   for dependencies required for running tests and building documentation.

My code does not require any changes to dependencies. This may be no problem.


