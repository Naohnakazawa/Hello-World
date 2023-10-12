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
# backend 
from qiskit_aer import AerSimulator
from qiskit.providers.fake_provider import FakePerth

backend = AerSimulator.from_backend(FakePerth())

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

# mwc
from qiskit_experiments.library import CorrelatedReadoutError

qubits = [0,1,2]
exp = CorrelatedReadoutError(physical_qubits=qubits, backend=backend)

for circ in exp.circuits():
    print(circ)

shots=1000
exp.set_run_options(shots=shots)
exp.analysis.set_options(plot=True)
result = exp.run()
mitigator = result.analysis_results(0).value

result.figure(0)
```

Note that the noise_model defined from a FakeBackend does not support correlated-reoudout-error.





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/da36777c-9b78-4cc8-a1f0-4f458a64021f)






