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

```








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
library = FixedFrequencyTransmon(default_values={"duration": 320})
cals = Calibrations.from_backend(backend, libraries=[library])

from qiskit_experiments.library import RoughDragCal
cal_drag = RoughDragCal([qubit], cals, backend=backend, betas=np.linspace(-20, 20, 25))
cal_drag.set_experiment_options(reps=[3, 5, 7])
cal_drag.circuits()[5].draw(output='mpl')

drag_data = cal_drag.run().block_for_results()
drag_data.figure(0)

```




