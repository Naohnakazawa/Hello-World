### T2Hahn-echo-(0,1,2)-backend-ibm-kyoto
```
# Note: T2 of qubit=78 on ibm_kyoto is 70 μs

# backend
from qiskit_ibm_runtime import QiskitRuntimeService
from qiskit_experiments.framework import ExperimentData

service = QiskitRuntimeService(channel="ibm_quantum")
backend = service.backend("ibm_kyoto")
qubit=78

# mwc

import numpy as np
import qiskit.pulse as pulse
from qiskit.circuit import QuantumCircuit, Gate, Parameter
from qiskit.circuit.library import RZXGate
from qiskit.compiler import schedule, transpile
from qiskit.pulse import Schedule, ScheduleBlock, InstructionScheduleMap

from qiskit_experiments.library.characterization.t2hahn import T2Hahn

conversion_factor = 1e-6
delays = list(range(0, 50, 1))
delays = [float(_)*conversion_factor for _ in delays]
number_of_echoes = 0

exp = T2Hahn(physical_qubits=(qubit, ),
             delays=delays,
             num_echoes=number_of_echoes,
             backend=backend)

exp.set_run_options(shots=100)

for i in range(3):
    display(exp.circuits()[i].draw("mpl"))
    
circ_t = transpile(exp.circuits()[2], backend=backend, optimization_level=3, initial_layout=[qubit])

exp_sched = schedule(circ_t, backend=backend)
display(exp_sched.draw())    

step1 = False
if step1 == True:
    
    exp_data = exp.run().block_for_results()
    display(exp_data.figure(0))
    exp_data.analysis_results(dataframe=True)
        
else:
    pass

# retrieve your jobs
# List of job IDs for the experiment
job_ids= ["ct87kd5qsez0008a174g"]

experiment = exp
expdata = ExperimentData(experiment = experiment)
expdata.add_data([service.job(job_id).result() for job_id in job_ids])
experiment.analysis.run(expdata, replace_results=True)

# Block execution of subsequent code until analysis is complete
expdata.block_for_results()

display(expdata.figure(0))
expdata.analysis_results(dataframe=True)
```



![image](https://github.com/user-attachments/assets/942280be-cd7d-4d7a-9a62-cd9794a372a7)





![image](https://github.com/user-attachments/assets/dc7d9c4a-6c31-4ad5-a493-bbaf407c479f)



```
number_of_echoes = 1
job_ids= ["ct879wfykbsg008yy8ng"]
```





![image](https://github.com/user-attachments/assets/e081a0ec-6835-400d-a4e9-7ca400f12292)






![image](https://github.com/user-attachments/assets/86a8a80c-3c36-4dd2-b822-8ce4a1ee4576)






```
number_of_echoes = 2
job_ids= ["ct87gktcvhg0008wngtg"]
```





![image](https://github.com/user-attachments/assets/9f34432a-2727-4b1f-8263-f41feb836712)








![image](https://github.com/user-attachments/assets/5c551e0b-a2c7-4e48-b9da-ad2fcaccc5a6)






