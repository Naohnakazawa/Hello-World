# Calibration

### RoughFrequencyCal
```

# mwc

import pandas as pd
import numpy as np
import qiskit.pulse as pulse
from qiskit.circuit import Parameter
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit import schedule
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend

# backend

backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, noise=False, seed=100)

# mwc

from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon

qubit = 0
library = FixedFrequencyTransmon(default_values={"duration": 320})
cals = Calibrations.from_backend(backend, libraries=[library])

from qiskit_experiments.library.calibration.rough_frequency import RoughFrequencyCal

freq01_estimate = backend.defaults().qubit_freq_est[qubit]
frequencies = np.linspace(freq01_estimate-15e6, freq01_estimate+15e6, 51)
spec = RoughFrequencyCal([qubit], cals, frequencies, backend=backend)
spec.set_experiment_options(amp=0.005)

spec_data = spec.run().block_for_results()
spec_data.figure(0)

columns_to_show = ["parameter", "qubits", "schedule", "value", "date_time"]
pd.DataFrame(**cals.parameters_table(qubit_list=[qubit]))[columns_to_show]

```



![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/e9ffdec6-ffd8-48c5-9bf4-b5534e50652f)






### RoughAmplitudeCal
```

# mwc

import pandas as pd
import numpy as np
import qiskit.pulse as pulse
from qiskit.circuit import Parameter
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit import schedule
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend

# backend

backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, noise=False, seed=100)

# mwc

from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon

qubit = 0
library = FixedFrequencyTransmon(default_values={"duration": 320})
cals = Calibrations.from_backend(backend, libraries=[library])

from qiskit_experiments.library.calibration import RoughXSXAmplitudeCal
rabi = RoughXSXAmplitudeCal((qubit,), cals, backend=backend, amplitudes=np.linspace(-0.1, 0.1, 51))

rabi_data = rabi.run().block_for_results()
rabi_data.figure(0)

columns_to_show = ["parameter", "qubits", "schedule", "value", "date_time"]
pd.DataFrame(**cals.parameters_table(qubit_list=[qubit, ()], parameters="amp"))[columns_to_show]

```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/53fba2f9-9b94-4a7b-9389-1938af484fbc)







### RoughDragCal
```
# mwc

import pandas as pd
import numpy as np
import qiskit.pulse as pulse
from qiskit.circuit import Parameter
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit import schedule
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend

# backend

backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, noise=False, seed=100)

# mwc

from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon

qubit = 0
library = FixedFrequencyTransmon(default_values={"duration": 320, "amp": 0.03})
cals = Calibrations.from_backend(backend, libraries=[library])

from qiskit_experiments.library import RoughDragCal
cal_drag = RoughDragCal([qubit], cals, backend=backend, betas=np.linspace(-20, 20, 25))
cal_drag.set_experiment_options(reps=[3, 5, 7])

drag_data = cal_drag.run().block_for_results()
drag_data.figure(0)

columns_to_show = ["parameter", "qubits", "schedule", "value", "date_time"]
pd.DataFrame(**cals.parameters_table(qubit_list=[qubit, ()], parameters=("amp","β")))[columns_to_show]

```



![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/6c00caca-29c5-4f0d-af44-3ecf77eb1aff)







### FineAmplitudeCal
```
# mwc

import numpy as np
import qiskit.pulse as pulse
from qiskit.circuit import Parameter
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit import schedule
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend

# backend

backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, noise=False, seed=100)

# mwc
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon

qubit = 0
library = FixedFrequencyTransmon(default_values={"duration": 320})
cals = Calibrations.from_backend(backend, libraries=[library])

from qiskit_experiments.library import FineSXAmplitudeCal

amp_cal = FineSXAmplitudeCal([qubit], 
                             cals, 
                             schedule_name="sx",
                             backend=backend, 
                             cal_parameter_name="amp", 
                             auto_update=True, 
                             )

exp_data_x90p = amp_cal.run().block_for_results()
exp_data_x90p.figure(0)

```


![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/33a4ca2c-64c1-4946-9569-6706a01c4ed2)







### FineDragCal
```
# section: example
# ..jupyter-execute::
# :hide-code:

#backend
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, noise=False, seed=100)

# ..jupyter-execute::

#mwc
import numpy as np
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon
from qiskit_experiments.library import FineDragCal

qubit = 0
library = FixedFrequencyTransmon(default_values={"duration": 320})
cals = Calibrations.from_backend(backend, libraries=[library])

exp = FineDragCal([qubit], 
                  cals, 
                  backend=backend, 
                  schedule_name="sx",
                  cal_parameter_name="β", 
                  auto_update=True, 
                  )
print(exp.circuits()[2])

# mwc
exp_data = exp.run().block_for_results()
exp_data.figure(0)

```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/c568dd07-1596-4860-887f-8edb8780900f)




### FineFrequencyCal
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend
from qiskit_experiments.framework import BackendData
from qiskit_experiments.test.mock_iq_backend import MockIQBackend
from qiskit_experiments.test.mock_iq_helpers import MockIQFineFreqHelper as FineFreqHelper

sx_duration=160
freq_shift_list=[-0.5e-6, -0.1e-6, 0.1e-6, 0.5e-6]
freq_shift=freq_shift_list[2]

exp_helper=FineFreqHelper(sx_duration, freq_shift)
backend=MockIQBackend(exp_helper)

# ..jupyter-execute::

# mwc
from qiskit.providers.fake_provider import FakePerth
from qiskit_experiments.library import FineFrequencyCal
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon
from qiskit_experiments.calibration_management import Calibrations

qubit=0
cals=Calibrations.from_backend(FakePerth(), libraries=[FixedFrequencyTransmon()])
freq_before=cals.get_parameter_value("drive_freq", qubit)
exp = FineFrequencyCal([qubit], cals, backend)

exp_data=exp.run().block_for_results()
freq_after=cals.get_parameter_value("drive_freq", qubit)
print(f" The frequency is {freq_before} Hz before the calibration.")
print(f" The calibrated frequency is {freq_after} Hz.")

exp_data.figure(0)
```

 The frequency is 5157546043.297998 Hz before the calibration.
 The calibrated frequency is 5157546052.551331 Hz.






 
![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/4cee4aca-f454-4ee5-8966-eb005af189bc)






### HalfAngleCal
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

# ..jupyter-execute::

# mwc
from qiskit import pulse
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon
from qiskit_experiments.library.calibration.half_angle_cal import HalfAngleCal

qubit = 0
library = FixedFrequencyTransmon(default_values={"duration": 640})
cals = Calibrations.from_backend(backend, libraries=[library])
exp = HalfAngleCal([qubit], cals, backend=backend)

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
exp.set_run_options(shots=1000, seed_simulator=177)

exp_data = exp.run().block_for_results()
result =exp_data.analysis_results()
exp_data.figure(0)

```






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/c3f2eada-994f-4cbf-a92f-459c96af9a1b)








### FrequencyCal
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm_nirobi
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_nairobi")
initial_layout = list(range(7))

# ..jupyter-execute::

# mwc
import numpy as np
from qiskit.providers.fake_provider import FakePerth
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon
from qiskit_experiments.library.calibration.frequency_cal import FrequencyCal

qubit = 0
delays =np.linspace(0, 10.0e-6, 11)
library = FixedFrequencyTransmon(default_values={"duration": 640})
cals = Calibrations.from_backend(backend, libraries=[library])

exp = FrequencyCal([qubit], 
                   cals, 
                   backend=backend,  
#                    cal_parameter_name = "drive_freq",   # may be bug!
                   delays=delays, 
                   osc_freq=2.e6, 
                   auto_update=True, 
                  )

exp_data = exp.run().block_for_results()
result=exp_data.analysis_results()
exp_data.figure(0)

```
