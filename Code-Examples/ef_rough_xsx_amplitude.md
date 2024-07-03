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
