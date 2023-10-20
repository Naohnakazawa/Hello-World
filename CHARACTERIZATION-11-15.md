# Characterization experiments 11,12,13,14,15


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

### CorrelatedReadoutError
```

# section: example
# ..jupyter-execute::
# :hide-code:

# backend & noise_model
from qiskit_aer import AerSimulator
from qiskit_aer.noise import NoiseModel
from qiskit.providers.fake_provider import FakePerth

noise_model = NoiseModel.from_backend(FakePerth(), 
                                      thermal_relaxation = False, 
                                      gate_error = False, 
                                      readout_error = True, 
                                     )

backend = AerSimulator.from_backend(FakePerth(), noise_model=noise_model)

# ..jupyter-execute::

# mwc
from qiskit_experiments.library import CorrelatedReadoutError

qubits = [0,1,2]
exp = CorrelatedReadoutError(physical_qubits=qubits, backend=backend)

for circ in exp.circuits():
    print(circ)

# ..jupyter-execute::

shots=1000
exp.set_run_options(shots=shots)
exp.analysis.set_options(plot=True)
result = exp.run()
mitigator = result.analysis_results(0).value

result.figure(0)

```

### HalfAngle
```

# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)
# note that "noise" option is "False"

# ..jupyter-execute::

# mwc
from qiskit import pulse
from qiskit_experiments.library.characterization import HalfAngle

qubit=0
exp = HalfAngle([qubit], backend=backend)
print(exp.circuits()[5])

# ..jupyter-execute::

# mwc
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
exp.set_run_options(shots=1000, seed_simulator=178)

exp_data = exp.run().block_for_results()
exp_data.figure(0)

```

### FineDrag
```

# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199) # "noise" option is "False"

# ..jupyter-execute::

# mwc
from qiskit_experiments.library.characterization import FineXDrag

qubit=0
exp = FineXDrag([qubit], backend=backend)
print(exp.circuits()[3])

# ..jupyter-execute::

# mwc
exp.set_run_options(shots=1000, seed_simulator=101)
exp_data = exp.run().block_for_results()
exp_data.figure(0)

```

### ReadoutAngle
```

# section: example
# ..jupyter-execute::
# :hide-code:

# backend  
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199) # "noise" option is "False"

# ..jupyter-execute::

# mwc
from qiskit_experiments.library.characterization import ReadoutAngle
qubit=0
exp = ReadoutAngle([qubit], backend=backend)
exp.set_run_options(shots=1000, seed_simulator=106)

exp_data = exp.run().block_for_results()
result = exp_data.analysis_results()
for _ in result:
    print(_)

# ..jupyter-execute::

# mwc
exp_data.figure(0)

```
































