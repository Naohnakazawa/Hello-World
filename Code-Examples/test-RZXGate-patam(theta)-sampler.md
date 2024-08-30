### changing the parameter(theta) in RZXGate
```
# backend

from qiskit_aer import AerSimulator
backend = AerSimulator()

# mwc

import numpy as np
from qiskit.circuit import QuantumCircuit, Parameter
from qiskit.transpiler.preset_passmanagers import generate_preset_pass_manager
from qiskit_ibm_runtime import Session, SamplerV2 as Sampler, QiskitRuntimeService

theta = Parameter("theta")
dthetas = np.linspace(- 3., 3., 61)
thetas = [np.pi / 4 + dtheta for dtheta in dthetas]

print(len(thetas))

circ = QuantumCircuit(2)   
circ.rzx(theta, 0, 1)
circ.measure_all()
    
display(circ.draw("mpl"))

pm = generate_preset_pass_manager(backend=backend, optimization_level=1)

shots =10000
pubs = []
for idx, value in enumerate(thetas):
    isa_circ = pm.run(circ)
    pubs.append((isa_circ, value, shots))

print(len(pubs))

sampler = Sampler(mode=backend)
jobs = sampler.run(pubs)

print(f">>> Job ID: {jobs.job_id()}")
print(f">>> Job Status: {jobs.status()}")

results = jobs.result()

# visualization 

import matplotlib.pyplot as plt

counts_10_list = []
counts_00_list = []
probs_10_list = []
probs_00_list = []
for idx, pub_result in enumerate(results):

    counts = pub_result.data.meas.get_counts()
    counts_10 = counts.get("10")
    counts_00 = counts.get("00")
    if counts_10 == None:
        counts_10 = 0
        
        if counts_00 == None:
            counts_00 = 0
        
        else:
            pass

    elif counts_00 == None:
        counts_00 == 0

    else:
        pass

    counts_10_list.append(counts_10)
    counts_00_list.append(counts_00)
    probs_10_list.append(counts_10 / shots)
    probs_00_list.append(counts_00 / shots)

x = thetas
y_10 = probs_10_list
y_00 = probs_00_list
fig, ax = plt.subplots()

# figure style
ax.plot(x, y_10, "bo", label="'10'")
ax.plot(x, y_00, "ro", label="'00'")

ax.set(xlim=(thetas[0], thetas[-1]),
       ylim=(0., 1.)
      )

ax.set_xlabel("theta")
ax.set_ylabel("prob_**")
ax.set_title("test-RZXGate-AerSimulator")

ax.legend()
plt.show()
```






![image](https://github.com/user-attachments/assets/a5342ee0-8be3-471c-8b40-43cbeacd6ba2)







![image](https://github.com/user-attachments/assets/2f6c484c-04bb-4abe-86fa-03d4dddfad5a)





