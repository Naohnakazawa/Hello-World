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
git config
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
# backend ibm_kyoto
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_kyoto")

#mwc
from qiskit_experiments.library import LocalReadoutError

qubits = [10,11,12]
num_qubits = len(qubits)
exp = LocalReadoutError(physical_qubits=qubits, backend=backend)
exp.set_run_options(shots=100)
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
# job_ids=["cpgb4e9btqp0008tsp40"] # In case : shots=1000

job_ids=["cpgdp28ja3gg00817x6g"] # list of job IDs for the experiment
exp_data = ExperimentData(experiment=exp)
exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp.analysis.run(exp_data)
exp_data.block_for_results()
result=exp_data.analysis_results()

exp_data.figure(0)
```






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/bcba1d8f-01f6-4596-9937-3f9ead1703b6)







### CorrelatedReadoutError
```
# backend ibm_brisbane
from qiskit_ibm_provider import IBMProvider
INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_brisbane")

# mwc
from qiskit_experiments.library import CorrelatedReadoutError

qubits = [10,11]
num_qubits = len(qubits)
exp = CorrelatedReadoutError(physical_qubits=qubits, backend=backend)
exp.set_run_options(shots=100)
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

# retrieve your jobs
from qiskit_experiments.framework import ExperimentData 

job_ids= ["cpgdwf9mbjng008qsqt0"] # In case : backend=ibm_brisbane, shots=100, physical_qubits=(10, 11), date=20140112
exp_data = ExperimentData(experiment=exp)
exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp.analysis.run(exp_data)
exp_data.block_for_results()
result=exp_data.analysis_results()

exp_data.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/706bd23c-9c81-4b0a-b0d4-20a4f5b1555b)








### Issue#1238(also #1221)

### reno

features-expclass:
  - |
    Added minimal working code examples to the API pages for each
    experiment, especially in characterizaion experiments.
    The minimal working code example is a code snippet which users can copy
    and paste to run the experiment. Users are required to specify a backend
    in the code example. As a default, the backend is a simulator such as
    `GenericBackendV2(num_qubits=27)`. In some experiments,
    the backend is an IBM real device due to the `backend.defaults()` dependence
    of the code example.

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

tox -e docs                      : well-done

2. The API documentation has been updated accordingly.

yes

3. You have updated the relevant documentation or written new docs. In case the PR needs
   to be merged without delay (e.g. for a high priority fix), open an issue for updating
   or adding the documentation later.

yes

4. You've added tests that cover the changes you've made, if relevant.

I've not added any tests. This is no problem.

5. If your change has an end user facing impact (new feature, deprecation, removal,
   etc.), you've added or updated a reno release note for that change and tagged the PR
   for the changelog.

I've added a reno release notes which describes the new feature.

6. If your code requires a change to dependencies, you've updated the corresponding
   requirements file: `requirements.txt` contain core dependencies,
   `requirements-extras.txt` for dependencies for optional features, and `requirements-dev.txt`
   for dependencies required for running tests and building documentation.

My code does not require any changes to dependencies. This is no problem.


