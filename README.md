# Hello-World

This is **Naohito Nakazawa's** first repository on github. *I am*

## *a physicist* 
### a part-time lecturer in Waseda University

a begnner of github and also, 
a begnner of python

This is a <sub>subscript</sub> text.

This is a <sup>superscript</sup> test.

Today is a **holiday**.
> Today is a **holiday**.

Some basic Git commands are:
```
git status
git add
git diff 
git commit
git log 
git checkout # dangerous!
git restore  # dangerous!
```

An interesting paper is [here](https://arxiv.org/abs/2308.02321).




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





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/88e75bfd-6eab-4f18-b6b4-dd67df46036b)







### FineFrequency
```
# backend
from qiskit_experiments.framework import BackendData
from qiskit_experiments.test.mock_iq_backend import MockIQBackend
from qiskit_experiments.test.mock_iq_helpers import MockIQFineFreqHelper as FineFreqHelper


sx_duration=160
freq_shift_list=[-0.5e-6, -0.1e-6, 0.1e-6, 0.5e-6]
freq_shift=freq_shift_list[0]

exp_helper=FineFreqHelper(sx_duration, freq_shift)
backend=MockIQBackend(exp_helper)
exp_helper_dt=BackendData(backend).dt

# mwc
import numpy as np
from qiskit import pulse
from qiskit.pulse import InstructionScheduleMap, DriveChannel
from qiskit_experiments.library import FineFrequency


qubit=0
inst_map = InstructionScheduleMap()
with pulse.build(name="sx") as sched_build:
    pulse.play(pulse.Gaussian(duration=sx_duration, amp=0.5, sigma=40), pulse.DriveChannel(0))
inst_map.add("sx", qubit, sched_build)

exp = FineFrequency([qubit], sx_duration, backend)
exp.set_transpile_options(inst_map=inst_map)

# mwc

exp.set_run_options(shots=1000, seed_simulator=175)
exp_data = exp.run().block_for_results()

d_theta = exp_data.analysis_results(1).value.n
print(d_theta)

d_freq = d_theta / (2*np.pi*sx_duration*exp_helper_dt)
print(d_freq)

exp_data.figure(0)
```



![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/088dfde6-90d6-49e3-bfb8-2c81d204cf8b)





### FineFrequencyCal
```
# backend
from qiskit_experiments.framework import BackendData
from qiskit_experiments.test.mock_iq_backend import MockIQBackend
from qiskit_experiments.test.mock_iq_helpers import MockIQFineFreqHelper as FineFreqHelper

sx_duration=160
freq_shift_list=[-0.5e-6, -0.1e-6, 0.1e-6, 0.5e-6]
freq_shift=freq_shift_list[2]

exp_helper=FineFreqHelper(sx_duration, freq_shift)
backend=MockIQBackend(exp_helper)
exp_helper_dt=BackendData(backend).dt

# mwc
import numpy as np
from qiskit import pulse
from qiskit.pulse import InstructionScheduleMap, DriveChannel

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





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/bca1b1f7-5a34-4fe5-ae6e-ea54a2784b8e)

