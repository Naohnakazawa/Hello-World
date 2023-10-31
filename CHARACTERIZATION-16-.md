# Characterization experiments 16, 17, 18

### Fine Frequency
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
freq_shift=freq_shift_list[0]

exp_helper=FineFreqHelper(sx_duration, freq_shift)
backend=MockIQBackend(exp_helper)
exp_helper_dt=BackendData(backend).dt

# ..jupyter-execute::

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
exp.set_run_options(shots=1000, seed_simulator=175)
exp_data = exp.run().block_for_results()

d_theta = exp_data.analysis_results(1).value.n
print(d_theta)
d_freq = d_theta / (2*np.pi*sx_duration*exp_helper_dt)
print(d_freq)

exp_data.figure(0)

```
