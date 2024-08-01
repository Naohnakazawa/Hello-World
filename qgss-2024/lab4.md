### lab4
```
# retrieve your jobs

all_job_data = {}
all_job_data["Anisotropic"] = service.job("ctm64as907y0008w1ngg").result()
all_job_data["XXX"] = service.job("ctm64c96g3rg00875g50").result()

# Your code for analyzing results goes here

import numpy as np
import matplotlib.pyplot as plt

h_vals = np.linspace(0., np.pi/2, 12)

num_spins = 50
primitive_results = all_job_data["Anisotropic"]

# plot "expectation value - qubit index"
data_x = range(num_spins)
labels = ["h=0.", "h=0.14279967","h=0.28559933","h=0.428399 ","h=0.57119866","h=0.71399833",
          "h=0.856798","h=0.99959766","h=1.14239733","h=1.28519699","h=1.42799666","h=1.57079633"]
colours = ["bo", "bx", "b^",  "go", "gx", "g^", "yo", "yx", "y^", "ro", "rx", "r^"]
for idx in range(len(h_vals)):
    plt.plot(data_x, primitive_results[idx].data.evs, colours[idx], label=labels[idx])

plt.xlabel("qubit index")
plt.ylabel("expectation value")

plt.legend(loc="upper left")
plt.show()

# Your code for analyzing results goes here

import numpy as np
import matplotlib.pyplot as plt

h_vals = np.linspace(0., np.pi/2, 12)

num_spins = 50
primitive_results = all_job_data["XXX"]

# plot "expectation value - qubit index"
data_x = range(num_spins)
labels = ["h=0.", "h=0.14279967","h=0.28559933","h=0.428399 ","h=0.57119866","h=0.71399833",
          "h=0.856798","h=0.99959766","h=1.14239733","h=1.28519699","h=1.42799666","h=1.57079633"]
colours = ["bo", "bx", "b^",  "go", "gx", "g^", "yo", "yx", "y^", "ro", "rx", "r^"]
for idx in range(len(h_vals)):
    plt.plot(data_x, primitive_results[idx].data.evs, colours[idx], label=labels[idx])

plt.xlabel("qubit index")
plt.ylabel("expectation value")

plt.legend(loc="lower left")
plt.show()

# plot " averaged expectation value - error mitigation method"
import numpy as np
import matplotlib.pyplot as plt

h_vals = np.linspace(0., np.pi/2, 12)
num_spins = 50
primitive_results_A = all_job_data["Anisotropic"]
primitive_results_X = all_job_data["XXX"]

data_x = h_vals

pub_results_av_evs = []
pub_results_av_stds = []
for idx, result in enumerate(primitive_results_A):
    av_ev = sum(result.data.evs) / num_spins
    av_std = sum(result.data.stds) / num_spins
    pub_results_av_evs.append(av_ev)
    pub_results_av_stds.append(av_std)

data_y_A = pub_results_av_evs

pub_results_av_evs = []
pub_results_av_stds = []
for idx, result in enumerate(primitive_results_X):
    av_ev = sum(result.data.evs) / num_spins
    av_std = sum(result.data.stds) / num_spins
    pub_results_av_evs.append(av_ev)
    pub_results_av_stds.append(av_std)

data_y_X = pub_results_av_evs


plt.plot(data_x, data_y_A, "bo", label="Anisotropic phase")
plt.plot(data_x, data_y_X, "r^", label="XXX phase")
plt.xlabel("external field 'h'")
plt.ylabel("averaged expectation value on 50 spins")
               
plt.show()
```







![image](https://github.com/user-attachments/assets/7f01d7a0-ebc5-4822-98e2-6d63b718fbf2)












![image](https://github.com/user-attachments/assets/4f0fd7cf-c913-4310-bcb4-5f94166032c0)











![image](https://github.com/user-attachments/assets/ae0cb0f3-eeac-4e31-94c5-312be80295c6)










