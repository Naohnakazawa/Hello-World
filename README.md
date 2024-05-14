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
    
step1=False # "False" shows the result saved in the previous run.
if step1==True:
    exp_data = exp.run().block_for_results()

else:
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
    exp_data = exp.run().block_for_results()

else:
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



