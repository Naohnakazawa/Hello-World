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

Note that the noise_model defined from a FakeBackend does not support correlated-reoudout-error.





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/da36777c-9b78-4cc8-a1f0-4f458a64021f)





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





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/29c9e663-cf3d-4bbe-b224-739d4bb83008)





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




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/494e35b9-a807-421c-834f-f936e55097aa)





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

print(exp.circuits()[0])
print()
print(exp.circuits()[1])

# ..jupyter-execute::

# mwc
exp.set_run_options(shots=1000, seed_simulator=106)
exp_data = exp.run().block_for_results()
result = exp_data.analysis_results()
for _ in result:
    print(_)

# ..jupyter-execute::

# mwc
exp_data.figure(0)

```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/919260d5-8037-40a2-b145-908bd8c8c76b)




