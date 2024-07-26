### lab3
```
# Your code for analyzing results goes here

import numpy as np
import matplotlib.pyplot as plt

# Start here, retrieve your jobs
from qiskit_ibm_runtime import QiskitRuntimeService

# service = QiskitRuntimeService()
service = QiskitRuntimeService(channel="ibm_quantum")

# backend = service.least_busy(min_num_qubits=127)
backend = service.backend("ibm_kyoto")

num_qubits = 50
job_ids = ["cthgtpax7b5g008zs6y0",
           "cthgtqa4gzyg008yry00",
           "cthgtrbsj17g00819gqg",
           "cthjd5w4gjh0008nr9b0",
           "cthjd74907y0008vj7k0",
           "cthjd7w907y0008vj7kg",
           "cthjnn66g3rg0086pasg",
          ]

primitive_results = []
# job_id
for job_id in job_ids:
    primitive_results.append(service.job(job_id).result()) 

results = [result for result in primitive_results]
pub_results = [result[0] for result in results]

# plot "expectation value - qubit index"
data_x = range(num_qubits)
data_y_0 = pub_results[0].data.evs
data_y_1 = pub_results[1].data.evs
data_y_2 = pub_results[2].data.evs
data_y_3 = pub_results[3].data.evs
data_y_4 = pub_results[4].data.evs
data_y_5 = pub_results[5].data.evs
data_y_6 = pub_results[6].data.evs

plt.plot(data_x, data_y_0, "bo", label="No")
plt.plot(data_x, data_y_1, "bx", label="DD")        
plt.plot(data_x, data_y_2, "r^", label="TREX")
# plt.plot(data_x, data_y_3, "rx-", label="ZNE (Exponential)")
plt.plot(data_x, data_y_4, "rx", label="ZNE (Linear)")
plt.plot(data_x, data_y_5, "gs", label="ZNE + Gate Twirling")
plt.plot(data_x, data_y_6, "gx", label="All")

plt.xlabel("qubit index")
plt.ylabel("expectation value")

plt.legend(loc="lower left")
plt.show()
```




![image](https://github.com/user-attachments/assets/ddaf8bcb-7648-4658-817b-9fc92ebfc05b)





```
# ZNE (Exponential)

plt.plot(data_x, data_y_3, "rx-", label="ZNE (Exponential)")

plt.xlabel("qubit index")
plt.ylabel("expectation value")

plt.legend(loc="upper left")
plt.show()
```




![image](https://github.com/user-attachments/assets/e9127f03-d486-4cd8-8a9e-a8fd90a618c1)








