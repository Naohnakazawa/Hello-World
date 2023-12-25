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








AnalysisResult
- name: @Parameters_ResonanceAnalysis
- value: CurveFitResult:
 - fitting method: least_squares
 - number of sub-models: 1
  * F_lorentzian(x) = a * abs(kappa) / sqrt(kappa**2 + 4 * (x - freq)**2) + b
 - success: True
 - number of function evals: 60
 - degree of freedom: 47
 - chi-square: 0.20807725698898097
 - reduced chi-square: 0.004427175680616616
 - Akaike info crit.: -272.58524512992307
 - Bayesian info crit.: -264.85794259902576
 - init params:
  * a = 0.9722961713217474
  * kappa = 6000000.0
  * freq = 4858496911.279
  * b = 0.027703828678252624
 - fit params:
  * a = 1.2189157032536997 ± 0.04469899139544198
  * kappa = 2648055.1916561713 ± 238228.09027489013
  * freq = 4859145715.26067 ± 77693.31150115609
  * b = -0.14312156990356453 ± 0.02064295728132438
 - correlations:
  * (kappa, b) = -0.7211241966179786
  * (a, kappa) = -0.4506950967764161
  * (a, b) = -0.14419656775417847
  * (kappa, freq) = -0.00040423232059015325
  * (a, freq) = 8.977458506629631e-05
  * (freq, b) = 0.00042908378660460597
- quality: good
- device_components: ['Q13']
- verified: False
AnalysisResult
- name: f01
- value: (4.85915+/-0.00008)e+09
- χ²: 0.004427175680616616
- quality: good
- extra: <1 items>
- device_components: ['Q13']
- verified: False



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

import pandas as pd

from qiskit_experiments.library import FineFrequencyCal
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon
from qiskit_experiments.calibration_management import Calibrations

qubit=13

cals=Calibrations.from_backend(backend, libraries=[FixedFrequencyTransmon()])
exp_cal = FineFrequencyCal((qubit,), cals, backend)
exp_cal.set_run_options(shots=1000)
print(exp_cal.circuits()[0])
print(exp_cal.circuits()[39])

columns_to_show = ["parameter", "qubits", "schedule", "value", "date_time"]
print()
print("parameters before calibration")
print(pd.DataFrame(**cals.parameters_table(qubit_list=[qubit, ()]))[columns_to_show])

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

print()
print("parameters after calibration")
print(pd.DataFrame(**cals.parameters_table(qubit_list=[qubit, ()]))[columns_to_show])

cal_data.figure(0)
```



parameters before calibration
    parameter qubits schedule         value                        date_time
0           β     ()       sx  0.000000e+00  2023-12-13 00:14:28.915828+0900
1           β     ()        x  0.000000e+00  2023-12-13 00:14:28.915797+0900
2  drive_freq  (13,)     None  5.005809e+09  2023-12-13 00:14:30.371949+0900
3           σ     ()        x  4.000000e+01  2023-12-13 00:14:28.915806+0900
4           σ     ()       sx  4.000000e+01  2023-12-13 00:14:28.915836+0900
5         amp     ()       sx  2.500000e-01  2023-12-13 00:14:28.915813+0900
6         amp     ()        x  5.000000e-01  2023-12-13 00:14:28.915752+0900
7    duration     ()        x  1.600000e+02  2023-12-13 00:14:28.915787+0900
8    duration     ()       sx  1.600000e+02  2023-12-13 00:14:28.915821+0900
9   meas_freq  (13,)     None  7.169739e+09  2023-12-13 00:14:32.215174+0900

parameters after calibration
    parameter qubits schedule         value                        date_time
0           β     ()       sx  0.000000e+00  2023-12-13 00:14:28.915828+0900
1           β     ()        x  0.000000e+00  2023-12-13 00:14:28.915797+0900
2  drive_freq  (13,)     None  5.005801e+09  2023-12-13 00:14:39.000361+0900
3           σ     ()        x  4.000000e+01  2023-12-13 00:14:28.915806+0900
4           σ     ()       sx  4.000000e+01  2023-12-13 00:14:28.915836+0900
5         amp     ()       sx  2.500000e-01  2023-12-13 00:14:28.915813+0900
6         amp     ()        x  5.000000e-01  2023-12-13 00:14:28.915752+0900
7    duration     ()        x  1.600000e+02  2023-12-13 00:14:28.915787+0900
8    duration     ()       sx  1.600000e+02  2023-12-13 00:14:28.915821+0900
9   meas_freq  (13,)     None  7.169739e+09  2023-12-13 00:14:32.215174+0900





 
![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/8a92aee2-a7f9-4220-a679-3418778fe641)







AnalysisResult
- name: @Parameters_ErrorAmplificationAnalysis
- value: CurveFitResult:
 - fitting method: least_squares
 - number of sub-models: 1
  * F_ping_pong(x) = amp / 2 * cos((d_theta + angle_per_gate) * x - phase_offset)...
 - success: True
 - number of function evals: 24
 - degree of freedom: 37
 - chi-square: 51.15420216199282
 - reduced chi-square: 1.3825460043781843
 - Akaike info crit.: 15.838607544556714
 - Bayesian info crit.: 20.90524590689852
 - init params:
  * amp = 0.4225774225774226
  * d_theta = -0.0017578953749166526
  * angle_per_gate = 1.5707963267948966
  * phase_offset = 0.0
  * base = 0.2257742257742258
 - fit params:
  * amp = 0.3981361888127638 ± 0.004283425471215865
  * d_theta = -0.003974338825298179 ± 0.0006193597741482609
  * angle_per_gate = 1.5707963267948966 ± 0.0
  * phase_offset = 0.0 ± 0.0
  * base = 0.21867191027714566 ± 0.0019232011683825059
 - correlations:
  * (amp, d_theta) = -0.09012144219293143
  * (d_theta, base) = -0.013041942976820634
  * (amp, base) = 0.7642820046921408
- quality: good
- device_components: ['Q13']
- verified: False
AnalysisResult
- name: d_theta
- value: -0.0040+/-0.0006
- χ²: 1.3825460043781843
- quality: good
- device_components: ['Q13']
- verified: False



### RoughDragCal
```
# section: example
# ..jupyter-execute::
# :hide-code:

#backend
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, noise=False, seed=100)

# ..jupyter-execute::

import pandas as pd
import numpy as np
import qiskit.pulse as pulse
from qiskit import schedule
from qiskit.circuit import Parameter
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon
from qiskit_experiments.library import RoughDragCal

qubit = 0
library = FixedFrequencyTransmon(default_values={"duration": 320, "amp": 0.03})
cals = Calibrations.from_backend(backend, libraries=[library])
cal_drag = RoughDragCal([qubit], cals, backend=backend, betas=np.linspace(-20, 20, 25))
cal_drag.set_experiment_options(reps=[3, 5, 7])

drag_data = cal_drag.run().block_for_results()
drag_data.figure(0)
```
