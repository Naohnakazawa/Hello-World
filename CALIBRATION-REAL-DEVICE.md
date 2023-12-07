# Calibration on a real device

### RoughFrequencyCal
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm_kyoto
from qiskit_ibm_provider import IBMProvider

INSTANCE = "ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_kyoto")

# ..jupyter-execute::

import numpy as np
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit_experiments.library.calibration.rough_frequency import RoughFrequencyCal

qubit = 13
cals = Calibrations.from_backend(backend)
freq_before=cals.get_parameter_value("drive_freq", qubit) 
frequencies = np.linspace(freq_before-15e6, freq_before+15e6, 51)

exp_cal = RoughFrequencyCal((qubit,), cals, frequencies, backend=backend)
exp_cal.set_experiment_options(amp=0.05)
exp_cal.set_run_options(shots=100)
print(exp_cal.circuits()[0])
print(exp_cal.circuits()[50])

step1=False # run carefully if your device is a real one!
if step1==True:
    
    cal_data = exp_cal.run()
    cal_result=cal_data.analysis_results()
    
    for _ in cal_result:
        print(_)
        
    cal_data.figure(0)

else:
    pass

# retrieve your jobs
from qiskit_experiments.framework import ExperimentData 

# job_ids=["cnrrmeh5s870008xq9wg"] # case: amp=0.005, shots=100

job_ids=["cnrys3cgsbh0008sytz0"] # list of job IDs for the experiment
cal_data = ExperimentData(experiment=exp_cal)
cal_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp_cal.analysis.run(cal_data)
cal_data.block_for_results()
cal_result=cal_data.analysis_results()
    
freq_after=cals.get_parameter_value("drive_freq", qubit)
print(f" The frequency is {freq_before} Hz before the calibration.")
print(f" The calibrated frequency is {freq_after} Hz.")

cal_data.figure(0)
```


The frequency is 4859096911.278802 Hz before the calibration.

The calibrated frequency is 4859145715.26067 Hz.
 




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/3c60dda7-fc3b-4f17-bb6a-0497cfdfe1df)







### FineFrequencyCal
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm_brisbane
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_brisbane")

# ..jupyter-execute::

# mwc
from qiskit_experiments.library import FineFrequencyCal
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon
from qiskit_experiments.calibration_management import Calibrations

qubit=13
cals=Calibrations.from_backend(backend, libraries=[FixedFrequencyTransmon()])
freq_before=cals.get_parameter_value("drive_freq", qubit) 
exp_cal = FineFrequencyCal((qubit,), cals, backend)
exp_cal.set_run_options(shots=1000)
print(exp_cal.circuits()[0])
print(exp_cal.circuits()[39])

step1=False
if step1==True:
    cal_data = exp_cal.run().block_for_results()
    cal_result = cal_data.analysis_results()    
    for _ in cal_result:
        print(_)

    cal_data.figure(0)

else:
    pass

# retrieve your jobs
from qiskit_experiments.framework import ExperimentData 

job_ids=["cnrzj0851cpg008ec2d0"] # list of job IDs for the experiment
cal_data = ExperimentData(experiment=exp_cal)
cal_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp_cal.analysis.run(cal_data)
cal_data.block_for_results()
cal_result=cal_data.analysis_results()

freq_after=cals.get_parameter_value("drive_freq", qubit)
print(f" The frequency is {freq_before} Hz before the calibration.")
print(f" The calibrated frequency is {freq_after} Hz.")

cal_data.figure(0)
```

The frequency is 5005804850.423292 Hz before the calibration.

The calibrated frequency is 5005796943.727423 Hz.





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/674b2d83-ab09-4a99-843a-3e50d6ffc6c7)

 

