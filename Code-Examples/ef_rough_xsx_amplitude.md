### EFRoughXSXAmplidudeCal(error)
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=105)

# ..jupyter-execute::

import pandas as pd
import numpy as np
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit_experiments.library.calibration import EFRoughXSXAmplitudeCal
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon

qubit = 0
library = FixedFrequencyTransmon(default_values={"duration": 80})
cals = Calibrations.from_backend(backend, libraries=[library])
exp_cal = EFRoughXSXAmplitudeCal((qubit,),
                                 cals,
                                 backend=backend,
                                 amplitudes=np.linspace(-0.5, 0.5, 51),
                                 ef_pulse_label="12",
                                )

columns_to_show = ["parameter", "qubits", "schedule", "value", "date_time"]
print("parameters before the calibration")
print(pd.DataFrame(**cals.parameters_table(qubit_list=[qubit, ()], parameters=("amp")))[columns_to_show])

cal_data = exp_cal.run().block_for_results()
cal_result=cal_data.analysis_results()
    
for _ in cal_result:
    print(_)

print("parameters after the calibration")
print(pd.DataFrame(**cals.parameters_table(qubit_list=[qubit, ()], parameters=("amp")))[columns_to_show])

cal_data.figure(0)
```

This code does not work on both STT and ibm_kyoto. The error mssage is like this,

TranspilerError: "HighLevelSynthesis was unable to synthesize Instruction(name='rzx01', num_qubits=2, num_clbits=0, params=[1.5707963267948966])."






### EFRoughXSXAmplitudeCal(revised, backend=STT)
```
# backend
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=True, seed=180)

# mwc
import numpy as np
import pandas as pd
import qiskit.pulse as pulse
from qiskit.compiler import schedule
from qiskit.circuit import Parameter
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon
from qiskit_experiments.library.calibration import EFRoughXSXAmplitudeCal

library = FixedFrequencyTransmon()
cals = Calibrations.from_backend(
                                backend=backend,
                                libraries=[library]
    )

amp = Parameter("amp")

with pulse.build(name="x12") as build_x12:
    with pulse.align_left():
        pulse.shift_frequency(-.25e9, pulse.DriveChannel(0))
        pulse.play(pulse.Drag(160,
                              amp,
                              40,
                              0.,
                              0.,
                             ), pulse.DriveChannel(0))
        
with pulse.build(name="sx12") as build_sx12:
    with pulse.align_left():
        pulse.shift_frequency(-.25e9, pulse.DriveChannel(0))
        pulse.play(pulse.Drag(160,
                              amp / 2,
                              40,
                              0.,
                              0.,
                             ), pulse.DriveChannel(0))

cals.add_schedule(build_x12, qubits=(0,), num_qubits=1)        
cals.add_schedule(build_sx12, qubits=(0,), num_qubits=1)
for sched in ["x", "x12"]:
    cals.add_parameter_value(0.5, "amp", (0,), schedule=sched)
    
for sched in ["sx", "sx12"]:
    cals.add_parameter_value(0.25, "amp", (0,), schedule=sched)

columns_to_show = ["parameter", "qubits", "schedule", "value", "date_time"]
pd.DataFrame(**cals.parameters_table(qubit_list=[0, ()]))[columns_to_show]

# default
exp_cal = EFRoughXSXAmplitudeCal(physical_qubits=(0,),
                                 calibrations=cals,
                                 amplitudes=np.linspace(-0.1, 0.1, 51),
                                 backend=backend,
                                 ef_pulse_label="12",)

print(exp_cal.calibrations)
print(exp_cal.circuits()[0])
print(exp_cal.circuits()[0].calibrations)

cal_data = exp_cal.run().block_for_results()
display(cal_data.figure(0))
cal_data.analysis_results(dataframe=True)
```






