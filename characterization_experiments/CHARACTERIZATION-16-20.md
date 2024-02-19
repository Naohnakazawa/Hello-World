# Characterization experiments 16, 17, 18, 19, 20

### Fine Frequency
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

from qiskit_experiments.library.characterization import FineFrequency

qubit=13
repetitions = list(range(40))
exp = FineFrequency((qubit,), 
                    delay_duration=320, 
                    backend=backend, 
                    repetitions=repetitions)
exp.set_run_options(shots=100, seed_simulator=199)
print(exp.circuits()[3])

step1=False # run carefully if your device is a real one
if step1==True:
    exp_data = exp.run().block_for_results()
    result = exp_data.analysis_results()
    
    for _ in result:
        print(_)
    
    exp_data.figure(0)

else:
    pass

# retrieve your jobs
from qiskit_experiments.framework import ExperimentData 

job_ids=["cnrj6gazeg00008v7nrg"] # list of job IDs for the experiment
exp_data = ExperimentData(experiment=exp)
exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp.analysis.run(exp_data)
exp_data.block_for_results()
result=exp_data.analysis_results()

import numpy as np
d_theta = exp_data.analysis_results(1).value.n
print(f"d_theta : {d_theta}")
dt = backend.dt
print(f"dt :{dt}")
d_freq = d_theta / (2 * np.pi * exp.experiment_options.delay_duration * dt)
print(f"d_freq : {d_freq}")

exp_data.figure(0)
```


d_theta : 0.004578346234158528

dt :5e-10

d_freq : 4554.165214703087







![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/2c9515d0-4bda-4950-9d92-6fc38e196ca9)






### Fine Frequency (new)
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend
from qiskit.providers.fake_provider import GenericBackendV2

num_qubits=27
backend =GenericBackendV2(num_qubits=num_qubits)

# ..jupyter-execute::
# mwc
from qiskit_experiments.library.characterization import FineFrequency

qubit=0
repetitions = list(range(40))
exp = FineFrequency((qubit,), 
                    delay_duration=320, 
                    backend=backend, 
                    repetitions=repetitions)
exp.set_transpile_options(optimization_level=0, basis_gates=['sx', 'rz', 'delay'])
exp.set_run_options(shots=1000, seed_simulator=199)
print(exp.circuits()[3])

# ..jupyter-execute::
# mwc
exp_data = exp.run().block_for_results()
result = exp_data.analysis_results()
exp_data.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/a2724a7d-1d79-483a-a6ba-6a9169303b58)







### RamseyXY
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
import numpy as np
from qiskit_experiments.library.characterization import RamseyXY

qubit=0
delays = np.linspace(0, 10.e-7, 101)
exp = RamseyXY([qubit], backend=backend, delays=delays, osc_freq=2.0e6)
exp.set_run_options(shots=1000, seed_simulator=201)
exp.circuits()[0].draw("mpl")

exp_data = exp.run().block_for_results()
result=exp_data.analysis_results()
exp_data.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/1844aef3-0e3b-40f5-bef1-32f36ad1cf02)






### ZZRamsey
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend
from qiskit.providers.fake_provider import FakePerth
from qiskit_aer import AerSimulator
from qiskit_aer.noise import NoiseModel

noise_model = NoiseModel.from_backend(FakePerth(), 
                                      thermal_relaxation=True, 
                                      gate_error=False, 
                                      readout_error=False,                                           
)

backend = AerSimulator.from_backend(FakePerth(), noise_model=noise_model)

# ..jupyter-execute::

#mwc
from qiskit_experiments.library.characterization import ZZRamsey

qubits = (0, 1)
exp = ZZRamsey(physical_qubits=qubits, backend=backend)
exp.set_run_options(shots=1000, seed_simulator=101)
print(exp.circuits()[2])
print(exp.circuits()[3])

exp_data = exp.run().block_for_results()
result=exp_data.analysis_results()
print(f"The value of {exp_data.analysis_results(1).name} is {exp_data.analysis_results(1).value}")

exp_data.figure(0)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/1b39765b-373d-4c50-868a-2d4e0989584d)








### ResonatorSpectroscopy
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_kyoto")

# ..jupyter-execute::

# mwc 
import numpy as np
from qiskit_experiments.library.characterization import ResonatorSpectroscopy

qubit = 13
exp = ResonatorSpectroscopy(physical_qubits = [qubit], backend = backend)
exp.set_run_options(shots=100, seed_simulator=119)

exp.circuit()[0].draw("mpl")

step1=False  # run carefully if your backend is a real device
if step1==True:
    exp_data = exp.run().block_for_results()
    result = exp_data.analysis_results()
    exp_data.figure(0)

# retrieve your jobs
from qiskit_experiments.framework import ExperimentData
job_ids= ["cnnm53we4ks0008gmpa0"] # list of job IDs for the experiment

exp_data = ExperimentData(experiment=exp)
exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp.analysis.run(exp_data)

exp_data.block_for_results()
result = exp_data.analysis_results()
exp_data.figure(0)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/e8a47195-03dd-421f-bf99-eff7cbbe9f4b)






### MultiStateDiscrimination
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

# ..jupyter-execute::

# mwc
from qiskit_experiments.library.characterization import MultiStateDiscrimination

qubit=0
exp=MultiStateDiscrimination([qubit], backend=backend)
exp_data=exp.run().block_for_results()
result=exp_data.analysis_results()
exp_data.figure(0)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/9efc11be-ad8f-450f-a001-76f5d42b286a)





