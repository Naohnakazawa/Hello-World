### Rabi
```
# backend STT
from qiskit_experiments.test import SingleTransmonTestBackend

backend = SingleTransmonTestBackend(5e9, -0.25e9, 1e9, 0.8e9, 1e4, noise=False)

# mwc
import numpy as np
from qiskit import pulse
from qiskit import QuantumCircuit
from qiskit.circuit import Gate, Parameter
from qiskit.pulse import ScheduleBlock
from qiskit.compiler import transpile, schedule

amplitudes = np.linspace(-0.1, 0.1, 21)
param = Parameter("amp")
gate = Gate(name="Rabi", num_qubits=1, params=[param])

with pulse.build() as build_sched:    
    pulse.play(pulse.Gaussian(160, param, 40), pulse.DriveChannel(0))
    
def template_circuit(physical_qubits):
    
    circuit = QuantumCircuit(1)
    circuit.append(gate, physical_qubits)
    circuit.add_calibration(gate, physical_qubits, build_sched, params=[param])
    
    return circuit, param

circs = []
for i, amp in enumerate(amplitudes):
    circuit, param = template_circuit((0,))
    
    amp = float(np.round(amp, decimals=6))    
    assigned_circ = circuit.assign_parameters({param: amp})    
    circs.append(assigned_circ)
    
counts_list = []
for i, qc in enumerate(circs):
    counts = backend.run(qc, shots=1000).result().results[0].data.counts
    if counts.get("0") == None:
        counts_list.append(0)
        
    else:
        counts_list.append(counts.get("0"))

import matplotlib.pyplot as plt

container = plt.plot(amplitudes, counts_list, "o")
plt.xlabel("amp")
plt.ylabel("counts")
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/43063dac-d25e-4898-adf1-1851d19205f4)




