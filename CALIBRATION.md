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

```
