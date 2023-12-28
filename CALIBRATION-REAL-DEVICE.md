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

# backend ibm_brisbane
from qiskit_ibm_provider import IBMProvider
INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_brisbane")

# ..jupyter-execute::

import pandas as pd
import numpy as np
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon
from qiskit_experiments.library import RoughDragCal


qubit = 13
library = FixedFrequencyTransmon(default_values={"duration": 320, "amp": 0.03})
cals = Calibrations.from_backend(backend, libraries=[library])
columns_to_show = ["parameter", "qubits", "schedule", "value", "date_time"]

print("parameters before the calibration")
print()
print(pd.DataFrame(**cals.parameters_table(qubit_list=[qubit, ()], parameters=("β")
                                    )
            )[columns_to_show])
print()

exp_cal = RoughDragCal((qubit,), cals, backend=backend, betas=np.linspace(-20, 20, 25))
exp_cal.set_experiment_options(reps=[3, 5, 7])
exp_cal.set_run_options(shots=1000)

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

job_ids=["cnscsp6ye0eg008avytg"] # list of job IDs for the experiment
cal_data = ExperimentData(experiment=exp_cal)
cal_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp_cal.analysis.run(cal_data)
cal_data.block_for_results()
cal_result=cal_data.analysis_results()
for _ in cal_result:    
    print(_)
    

print()
print("parameters after the calibration")
print()
print(pd.DataFrame(**cals.parameters_table(qubit_list=[qubit, ()], parameters=("β")
                                    )
            )[columns_to_show])

cal_data.figure(0)
```


parameters before the calibration

  parameter qubits schedule  value                        date_time
0         β     ()        x    0.0  2023-12-26 15:14:27.204100+0900
1         β     ()       sx    0.0  2023-12-26 15:14:27.204140+0900

AnalysisResult
- name: @Parameters_DragCalAnalysis
- value: CurveFitResult:
 - fitting method: least_squares
 - number of sub-models: 3
  * F_nrep=3(x) = amp * cos(2 * pi * 3 * freq * (x - beta)) + base
  * F_nrep=5(x) = amp * cos(2 * pi * 5 * freq * (x - beta)) + base
  * F_nrep=7(x) = amp * cos(2 * pi * 7 * freq * (x - beta)) + base
 - success: True
 - number of function evals: 72
 - degree of freedom: 71
 - chi-square: 74.5742961125217
 - reduced chi-square: 1.0503421987679111
 - Akaike info crit.: 7.573083362591369
 - Bayesian info crit.: 16.84303581673661
 - init params:
  * amp = -0.3086913086913087
  * freq = 0.0015237906414600883
  * beta = 15.789473684210527
  * base = 0.15434565434565434
 - fit params:
  * amp = -0.17148850470318458 ± 0.010766827218865974
  * freq = 0.002717487389177772 ± 0.00011743617189863139
  * beta = -0.3416700672833599 ± 0.09930589230541134
  * base = 0.18534997440753273 ± 0.0110458874780722
 - correlations:
  * (amp, base) = -0.9969360501814953
  * (freq, base) = -0.9806586557798257
  * (amp, beta) = -0.013136083141514831
  * (freq, beta) = -0.009213419496257444
  * (beta, base) = 0.013150028834662647
  * (amp, freq) = 0.9736514574119376
- quality: bad
- device_components: ['Q13']
- verified: False
AnalysisResult
- name: beta
- value: -0.34+/-0.10
- χ²: 1.0503421987679111
- quality: bad
- device_components: ['Q13']
- verified: False

parameters after the calibration

  parameter qubits schedule    value                        date_time
0         β     ()        x  0.00000  2023-12-26 15:14:27.204100+0900
1         β  (13,)        x -0.34167  2023-12-26 15:14:37.211674+0900
2         β     ()       sx  0.00000  2023-12-26 15:14:27.204140+0900






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/fc25609f-72f4-4495-be36-5122fc858b8a)






### FineDragCal
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

#mwc
import pandas as pd
import numpy as np
from qiskit_experiments.calibration_management.calibrations import Calibrations

qubit = 13
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon
from qiskit_experiments.library import FineDragCal
library = FixedFrequencyTransmon(default_values={"duration": 640, "amp": 0.3}) 
cals = Calibrations.from_backend(backend, libraries=[library]) 
columns_to_show = ["parameter", "qubits", "schedule", "value", "date_time"]

print("parameters before the calibration")
print(pd.DataFrame(**cals.parameters_table(qubit_list=[qubit, ()], parameters=("β")
                                    )
            )[columns_to_show]
     )

from qiskit_experiments.library import FineDragCal

exp_cal = FineDragCal([qubit], 
                       cals, 
                       backend=backend, 
                       schedule_name="sx",
                       cal_parameter_name="β", 
                       auto_update=True, 
                      )
exp_cal.set_run_options(shots=1000)
exp_cal.set_experiment_options()
exp_cal.set_transpile_options(optimization_lebel=3)
print(exp_cal.circuits()[2])

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

job_ids=["cp54wrkbtqp0008fr5d0"] # list of job IDs for the experiment
cal_data = ExperimentData(experiment=exp_cal)
cal_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp_cal.analysis.run(cal_data)
cal_data.block_for_results()
cal_result=cal_data.analysis_results()
for _ in cal_result:
    print(_)

print("parameters after the calibration")
print(pd.DataFrame(**cals.parameters_table(qubit_list=[qubit, ()], parameters=("β")
                                    )
            )[columns_to_show]
     )

    
cal_data.figure(0)
```



parameters before the calibration
  parameter qubits schedule  value                        date_time
0         β     ()       sx    0.0  2023-12-26 15:22:54.502610+0900
1         β     ()        x    0.0  2023-12-26 15:22:54.502587+0900

AnalysisResult
- name: @Parameters_ErrorAmplificationAnalysis
- value: CurveFitResult:
 - fitting method: least_squares
 - number of sub-models: 1
  * F_ping_pong(x) = amp / 2 * cos((d_theta + angle_per_gate) * x - phase_offset)...
 - success: True
 - number of function evals: 21
 - degree of freedom: 18
 - chi-square: 67.72654534042172
 - reduced chi-square: 3.7625858522456515
 - Akaike info crit.: 28.394918640588852
 - Bayesian info crit.: 30.386383187696833
 - init params:
  * amp = 0.2947052947052947
  * d_theta = 0.009546009546009546
  * angle_per_gate = 0.0
  * phase_offset = 1.5707963267948966
  * base = 0.1043956043956044
 - fit params:
  * amp = 0.2947052947052947 ± 0.0
  * d_theta = -0.019920278541275226 ± 0.0026820769604929445
  * angle_per_gate = 0.0 ± 0.0
  * phase_offset = 1.5707963267948966 ± 0.0
  * base = 0.11916722495645625 ± 0.00440556527127458
 - correlations:
  * (d_theta, base) = -0.8900953518342607
- quality: bad
- device_components: ['Q13']
- verified: False
AnalysisResult
- name: d_theta
- value: -0.0199+/-0.0027
- χ²: 3.7625858522456515
- quality: bad
- device_components: ['Q13']
- verified: False

parameters after the calibration
  parameter qubits schedule     value                        date_time
0         β     ()       sx  0.000000  2023-12-26 15:22:54.502610+0900
1         β  (13,)       sx  0.572388  2023-12-26 15:23:07.859917+0900
2         β     ()        x  0.000000  2023-12-26 15:22:54.502587+0900







![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/82bcd896-9159-4bc7-8e1a-cfbdd73dae65)






### FineXAmplitudeCal
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

import pandas as pd
import numpy as np
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit_experiments.library.calibration.fine_amplitude import FineXAmplitudeCal
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon

qubit=13
cals=Calibrations.from_backend(backend=backend, libraries=[FixedFrequencyTransmon()])
exp_cal = FineXAmplitudeCal(physical_qubits=(qubit,),
                            calibrations=cals, 
                            schedule_name="x",                            
                            backend=backend,
                            cal_parameter_name="amp", 
                            auto_update=True
                           )
exp_cal.set_run_options(shots=1000)

columns_to_show = ["parameter", "qubits", "schedule", "value", "date_time"]
print("parameters before the calibration")
print(pd.DataFrame(**cals.parameters_table(qubit_list=[qubit, ()], parameters=("amp")))[columns_to_show])

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

job_ids=["cnsc4xvvn4c0008a1n50"] # list of job IDs for the experiment
cal_data = ExperimentData(experiment=exp_cal)
cal_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp_cal.analysis.run(cal_data)
cal_data.block_for_results()
cal_result=cal_data.analysis_results()
for _ in cal_result:
    print(_)
    
print("parameters after the calibration")
print(pd.DataFrame(**cals.parameters_table(qubit_list=[qubit, ()], parameters=("amp")))[columns_to_show])

cal_data.figure(0)
```


parameters before the calibration

  parameter qubits schedule  value                        date_time
0       amp     ()        x   0.50  2023-12-26 23:50:02.822145+0900
1       amp     ()       sx   0.25  2023-12-26 23:50:02.822189+0900


AnalysisResult
- name: @Parameters_FineAmplitudeAnalysis
- value: CurveFitResult:
 - fitting method: least_squares
 - number of sub-models: 2
  * F_spam cal.(x) = amp / 2 * (2 * x - 1) + base
  * F_fine amp.(x) = amp / 2 * cos((d_theta + angle_per_gate) * x - phase_offset)...
 - success: True
 - number of function evals: 28
 - degree of freedom: 13
 - chi-square: 1056.224319423245
 - reduced chi-square: 81.24802457101886
 - Akaike info crit.: 73.03787429062339
 - Bayesian info crit.: 75.35564045734273
 - init params:
  * amp = 0.983016983016983
  * base = 0.49800199800199796
  * d_theta = -1.2566370614359172
  * angle_per_gate = 3.141592653589793
  * phase_offset = 1.5707963267948966
 - fit params:
  * amp = 1.0171024007384146 ± 0.0034148190529203213
  * base = 0.516333432702224 ± 0.001676774644105911
  * d_theta = -1.0511396877988275 ± 0.0010644728174618453
  * angle_per_gate = 3.141592653589793 ± 0.0
  * phase_offset = 1.5707963267948966 ± 0.0
 - correlations:
  * (amp, d_theta) = 0.10381524080319095
  * (amp, base) = 0.11034849064113972
  * (base, d_theta) = 0.4044364160621178
- quality: bad
- device_components: ['Q13']
- verified: False
AnalysisResult
- name: d_theta
- value: -1.0511+/-0.0011
- χ²: 81.24802457101886
- quality: bad
- device_components: ['Q13']
- verified: False


parameters after the calibration

  parameter qubits schedule     value                        date_time
0       amp  (13,)        x  0.751414  2023-12-26 23:50:17.413272+0900
1       amp     ()        x  0.500000  2023-12-26 23:50:02.822145+0900
2       amp     ()       sx  0.250000  2023-12-26 23:50:02.822189+0900





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/3850c6ca-8228-42cb-a4c0-faf753751374)








### RoughXSXAmplitudeCal
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

import pandas as pd
import numpy as np
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit_experiments.library.calibration import RoughXSXAmplitudeCal
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon

qubit = 13
library = FixedFrequencyTransmon(default_values={"duration": 320})
cals = Calibrations.from_backend(backend, libraries=[library])
exp_cal = RoughXSXAmplitudeCal((qubit,), cals, backend=backend, amplitudes=np.linspace(-0.1, 0.1, 21))
exp_cal.set_run_options(shots=1000)
print(exp_cal.circuits()[0])

columns_to_show = ["parameter", "qubits", "schedule", "value", "date_time"]
print("parameters before the calibration")
print(pd.DataFrame(**cals.parameters_table(qubit_list=[qubit, ()], parameters=("amp")))[columns_to_show])

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

job_ids=["cp5kxzqja3gg008hd8eg"] # list of job IDs for the experiment
cal_data = ExperimentData(experiment=exp_cal)
cal_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp_cal.analysis.run(cal_data)
cal_data.block_for_results()
cal_result=cal_data.analysis_results()
for _ in cal_result:
    print(_)

print("parameters after the calibration")
print(pd.DataFrame(**cals.parameters_table(qubit_list=[qubit, ()], parameters=("amp")))[columns_to_show])

cal_data.figure(0)
```


           ┌────────────┐ ░ ┌─┐
        q: ┤ Rabi(-0.1) ├─░─┤M├
           └────────────┘ ░ └╥┘
measure: 1/══════════════════╩═
                             0 


parameters before the calibration

  parameter qubits schedule  value                        date_time
0       amp     ()       sx   0.25  2023-12-28 17:14:59.943711+0900
1       amp     ()        x   0.50  2023-12-28 17:14:59.943667+0900
AnalysisResult
- name: @Parameters_OscillationAnalysis
- value: CurveFitResult:
 - fitting method: least_squares
 - number of sub-models: 1
  * F_cos(x) = amp * cos(2 * pi * freq * x + phase) + base
 - success: True
 - number of function evals: 72
 - degree of freedom: 17
 - chi-square: 28.278897321643
 - reduced chi-square: 1.6634645483319412
 - Akaike info crit.: 14.249461632877885
 - Bayesian info crit.: 18.427551383771576
 - init params:
  * amp = 0.5211417505095016
  * freq = 3.353174795467746
  * phase = 0.7853981633974483
  * base = 0.5211417505095016
 - fit params:
  * amp = -0.4997504329261023 ± 0.002134950319282326
  * freq = 6.62707661336081 ± 0.021586905025742495
  * phase = -0.00435602718165161 ± 0.008205472513654088
  * base = 0.5045734455678135 ± 0.0018974366818668503
 - correlations:
  * (amp, freq) = -0.10625458600228559
  * (freq, base) = -0.04159244048782998
  * (amp, phase) = 0.0016549176691514887
  * (phase, base) = 0.01206247253143645
  * (freq, phase) = 0.016356077932642658
  * (amp, base) = 0.2316548506103041
- quality: good
- device_components: ['Q13']
- verified: False
AnalysisResult
- name: rabi_rate
- value: 6.627+/-0.022
- χ²: 1.6634645483319412
- quality: good
- device_components: ['Q13']
- verified: False

parameters after the calibration

  parameter qubits schedule               value  \
0       amp  (13,)       sx  0.037724+0.000000j                                      
1       amp     ()       sx  0.250000+0.000000j   
2       amp     ()        x  0.500000+0.000000j   
3       amp  (13,)        x  0.075448+0.000000j   

                         date_time  
0  2023-12-28 17:15:12.459711+0900  
1  2023-12-28 17:14:59.943711+0900  
2  2023-12-28 17:14:59.943667+0900  
3  2023-12-28 17:15:12.455329+0900  







