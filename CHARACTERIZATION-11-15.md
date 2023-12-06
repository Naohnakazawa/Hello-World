# Characterization experiments 11,12,13,14,15


### LocalReadoutError
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_aer import AerSimulator
from qiskit.providers.fake_provider import FakePerth

backend = AerSimulator.from_backend(FakePerth())

# ..jupyter-execute::

# mwc
from qiskit_experiments.library import LocalReadoutError

qubits = list(range(4))
shots=10000

exp = LocalReadoutError(physical_qubits=qubits, backend=backend)
exp.analysis.set_options(plot=True)
exp.set_run_options(shots=shots)

result = exp.run()
mitigator = result.analysis_results(0).value

result.figure(0)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/7db60598-9587-41cd-964a-eaf450f872c4)





### CorrelatedReadoutError
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm_kyoto
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_kyoto")

# ..jupyter-execute::

#mwc
from qiskit_experiments.library import CorrelatedReadoutError

qubits = [10,11,12]
num_qubits = len(qubits)
exp = CorrelatedReadoutError(physical_qubits=qubits, backend=backend)
exp.set_run_options(shots=1000)
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

jobs_ids=[" "] # list of job IDs for the experiment
exp_data = ExperimentData(experiment=exp)
exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp.analysis.run(exp_data)
exp_data.block_for_results()
result=exp_data.analysis_results()

exp_data.figure(0)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/55967124-d1bf-427d-a1b8-82c7024d490f)






### HalfAngle
```

# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)
# note that "noise" option is "False"

# ..jupyter-execute::

# mwc
from qiskit import pulse
from qiskit_experiments.library.characterization import HalfAngle

qubit=0
exp = HalfAngle([qubit], backend=backend)
print(exp.circuits()[5])

# ..jupyter-execute::

# mwc
inst_map = backend.defaults().instruction_schedule_map
with pulse.build(backend=backend, name="y") as sched_build:
    pulse.play(pulse.Drag(duration=160, 
                          sigma=40, 
                          beta=5, 
                          amp=0.05821399464431249, 
                          angle=0.0, 
                          name="Yp_d0"
                         ), pulse.DriveChannel(0), name="Yp_d0")
inst_map.add("y", (qubit,), sched_build)    
exp.set_transpile_options(inst_map=inst_map)
exp.set_run_options(shots=1000, seed_simulator=178)

exp_data = exp.run().block_for_results()
exp_data.figure(0)

```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/41809e24-12e0-42dc-8478-5524309b9517)






### FineDrag
```

# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199) # "noise" option is "False"

# ..jupyter-execute::

# mwc
from qiskit_experiments.library.characterization import FineXDrag

qubit=0
exp = FineXDrag([qubit], backend=backend)
print(exp.circuits()[3])

# ..jupyter-execute::

# mwc
exp.set_run_options(shots=1000, seed_simulator=101)
exp_data = exp.run().block_for_results()
exp_data.figure(0)

```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/2eb727ab-2d3f-4283-8108-cf1fe168474d)





### ReadoutAngle
```

# section: example
# ..jupyter-execute::
# :hide-code:

# backend  
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199) # "noise" option is "False"

# ..jupyter-execute::

# mwc
from qiskit_experiments.library.characterization import ReadoutAngle
qubit=0
exp = ReadoutAngle([qubit], backend=backend)
exp.set_run_options(shots=1000, seed_simulator=106)

exp_data = exp.run().block_for_results()
result = exp_data.analysis_results()
for _ in result:
    print(_)

# ..jupyter-execute::

# mwc
exp_data.figure(0)

```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/11512a28-e388-4dd1-bf03-7748bcb2c5c2)






AnalysisResult
- name: readout_angle
- value: 1.027061422399426
- extra: <4 items>
- device_components: ['Q0']
- verified: False





















