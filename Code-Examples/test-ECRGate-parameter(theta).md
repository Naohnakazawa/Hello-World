### changing the parameter theta of ECRGate
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
dthetas = np.linspace(- 1., 1., 41)
thetas = [np.pi / 4 + dtheta for dtheta in dthetas]

print(len(thetas))

circ = QuantumCircuit(2)   
circ.rzx(theta, 0, 1)
circ.x(0)
circ.rzx(- theta, 0, 1)
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

counts_01_list = []
counts_11_list = []
probs_01_list = []
probs_11_list = []
for idx, pub_result in enumerate(results):

    counts = pub_result.data.meas.get_counts()
    counts_01 = counts.get("01")
    counts_11 = counts.get("11")
    if counts_01 == None:
        counts_01 = 0
        
        if counts_11 == None:
            counts_11 = 0
        
        else:
            pass

    elif counts_11 == None:
        counts_11 == 0

    else:
        pass

    counts_01_list.append(counts_01)
    counts_11_list.append(counts_11)
    probs_01_list.append(counts_01 / shots)
    probs_11_list.append(counts_11 / shots)

x = thetas
y_01 = probs_01_list
y_11 = probs_11_list
fig1, ax = plt.subplots()

# figure style
ax.scatter(x, y_01)
ax.scatter(x, y_11)

ax.set(xlim=(thetas[0], thetas[-1]),
       ylim=(0., 1.)
      )

ax.set_xlabel("theta")
ax.set_ylabel("prob_01")
ax.set_title("test-ECRGate-AerSimulator")

plt.show()
```





![image](https://github.com/user-attachments/assets/2e1a5030-066a-4e70-9bc0-10762cd571f9)







![image](https://github.com/user-attachments/assets/8fa1a7dc-c2f3-47b3-99a3-7a2e98cd1ad8)


