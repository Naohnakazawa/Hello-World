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
git config
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
# backend ibm_kyoto
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_kyoto")

#mwc
from qiskit_experiments.library import LocalReadoutError

qubits = [10,11,12]
num_qubits = len(qubits)
exp = LocalReadoutError(physical_qubits=qubits, backend=backend)
exp.set_run_options(shots=100)
exp.analysis.set_options(plot=True)

for c in exp.circuits():
    print(c)
    
step1=False # "False" shows the result saved in the previous run.
if step1==True:
    exp_data = exp.run().block_for_results()

else:
    # retrieve your jobs
    from qiskit_experiments.framework import ExperimentData 
    # job_ids=["cpgb4e9btqp0008tsp40"] # In case : shots=1000

    job_ids=["cpgdp28ja3gg00817x6g"] # list of job IDs for the experiment
    exp_data = ExperimentData(experiment=exp)
    exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
    exp.analysis.run(exp_data)
    exp_data.block_for_results()

result=exp_data.analysis_results()
exp_data.figure(0)
```






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/bcba1d8f-01f6-4596-9937-3f9ead1703b6)







### CorrelatedReadoutError
```
# backend ibm_brisbane
from qiskit_ibm_provider import IBMProvider
INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_brisbane")

# mwc
from qiskit_experiments.library import CorrelatedReadoutError

qubits = [10,11]
num_qubits = len(qubits)
exp = CorrelatedReadoutError(physical_qubits=qubits, backend=backend)
exp.set_run_options(shots=100)
exp.analysis.set_options(plot=True)
for c in exp.circuits():
    print(c)

step1=False # run carefully if your device is a real one!
if step1==True:
    exp_data = exp.run().block_for_results()

else:
    # retrieve your jobs
    from qiskit_experiments.framework import ExperimentData 

    job_ids= ["cpgdwf9mbjng008qsqt0"] # In case : backend=ibm_brisbane, shots=100, physical_qubits=(10, 11), date=20140112
    exp_data = ExperimentData(experiment=exp)
    exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
    exp.analysis.run(exp_data)
    exp_data.block_for_results()

result=exp_data.analysis_results()
exp_data.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/706bd23c-9c81-4b0a-b0d4-20a4f5b1555b)









### Plot Experiment Raw Data (T1)
```
# backend 
from qiskit_ibm_runtime.fake_provider import FakeManilaV2
from qiskit_aer import AerSimulator
backend = AerSimulator.from_backend(FakeManilaV2())

# mwc
import numpy as np
from qiskit_experiments.library.characterization import T1

qubit0_t1 = FakeManilaV2().qubit_properties(0).t1
print(qubit0_t1)

qubit=0
delays = np.arange(1.00e-6, 3*qubit0_t1, 30.0e-6)
exp = T1(physical_qubits=(qubit, ), delays=delays, backend=backend)

shots=50
exp.set_run_options(shots=shots)
exp_data=exp.run().block_for_results()
exp_data.figure(0)

# experiment-raw-data
data_raw = exp_data.data()
x_data=[]
y_data=[]
for i in range(len(exp_data.data())):
    x_data.append(data_raw[i]["metadata"]["xval"]*1000) # the unit of x_data is "ms"
    if data_raw[i]["counts"].get("1") == None:
        p1 = 0
    else:
        p1 = float(int(data_raw[i]["counts"]["1"]) / int(data_raw[i]["shots"]))
    y_data.append(p1)

x_min = x_data[0]
x_max = x_data[-1]
import matplotlib.pyplot as plt
import numpy as np

# plot raw-data with matplotlib.plot
x=x_data
y=y_data

fig1, ax = plt.subplots()

# figure style
ax.scatter(x, y)

ax.set(xlim=(x_min, x_max),
       ylim=(0., 1.))
ax.set_xlabel("Delay(ms)")
ax.set_ylabel("P(1)")
ax.set_title("T1-experiment")

plt.show()
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/7a512711-5d0c-4c40-8135-73aa6a16c07d)





### Least-Squares Fitting with Scipy (T1)
```
# test 

import numpy as np
import matplotlib.pyplot as plt
import scipy 
from scipy.optimize import least_squares

# fit model
def func(x, u, y):
    return x[0]*np.exp((- 1) * x[1] * u) + x[2] - y

# experiment-raw-data 
u_data = np.array([0.001, 0.031, 0.061, 0.091, 0.121,
             0.151, 0.181, 0.21099999999999997, 0.241, 0.27099999999999996, 0.301])
y_data = np.array([0.86, 0.66, 0.5, 0.42, 0.52, 0.24, 0.34, 0.2, 0.24, 0.18, 0.12])

# initial guess of fitting parameters
x0 = np.array([0.86, 1.00, 1.00])

# main
res = least_squares(fun=func, x0=x0, args=(u_data, y_data), verbose=2)
res.x
t1=(1 / res.x[1])* 1.e-3

# a solution with least-squares method
u_min = u_data[0]
u_max = u_data[-1]
u_val = np.linspace(u_min, u_max, 1001)
y_val = res.x[0]*np.exp((- 1) * res.x[1] * u_val) + res.x[2] 

print(f"The value of T1 is {t1} (s).")

# figure
plt.plot(u_data, y_data, "ob", label = "data")
plt.plot(u_val, y_val, "-r", label = "least_square")

plt.xlabel("Delay(ms)")
plt.ylabel("P(1)")
plt.title("T1-experiment")
plt.legend()
plt.show()
```

The value of T1 is 0.0001335043664939613 (s).






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/0f725cdf-a7c9-4352-a8ae-7b56456b258b)





### Plot Experiment Raw Data (ResonatorSpectroscopy)
```
# qiskit v1.0.0
# qiskit-experiments v0.6.0
# provicer=IBMProvider()
# backdend=provider.get_backend()
# provider.job_retrieve() # Retrieve your jobs wiht provicer=IBMProvider()

# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm-kyoto
from qiskit_ibm_provider import IBMProvider
provider = IBMProvider() 
backend = provider.get_backend("ibm_kyoto")

# ..jupyter-execute::

# mwc 
import numpy as np
import matplotlib.pyplot as plt
from qiskit_experiments.library.characterization import ResonatorSpectroscopy

qubit = 107
meas_freq_estimate = backend.defaults().meas_freq_est[qubit]
frequencies=np.linspace(meas_freq_estimate - 20.0e6, meas_freq_estimate + 20.0e6, 201)
exp = ResonatorSpectroscopy(physical_qubits = (qubit,),
                            backend = backend,
                            frequencies=frequencies,
                            absolute = True,
                           )
exp.set_run_options(shots=1000)

print(exp.circuits()[0])
print(backend.defaults().meas_freq_est[qubit])

step1=False  # "False" shows the result in the previous run
if step1==True:
    exp_data = exp.run().block_for_results()
    
else:
    # retrieve your jobs
    from qiskit_experiments.framework import ExperimentData
    # job_ids = ["cqs4qjy88ev00080zbj0"] # case: backend=ibm_kyoto, shots=1000, qubits=(107,), frequencies=default
    
    job_ids = ["cr5ta1r70xt0008gkr20"] # case: backend=ibm_kyoto, shots=1000, qubits=(107,), 
                                       # frequencies=np.linspace(meas_freq_estimate - 20.0e6, meas_freq_estimate + 20.0e6, 201)       
    exp_data = ExperimentData(experiment=exp)
    exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
    exp.analysis.run(exp_data)
    exp_data.block_for_results()
    
result = exp_data.analysis_results()
exp_data.figure(0)


# raw_data (measure_level=1, iq-data)
data_raw = exp_data.data()

iq_data=[]
u_data=[]
v_data=[]

for i in range(len(data_raw):    
    u, v=data_raw[i]["memory"][0]    
    iq=np.sqrt(u**2 + v**2)
    u_data.append(u)       
    v_data.append(v)
    iq_data.append(iq)
    
u_min = min(u_data)
u_max = max(u_data)
v_min = min(v_data)
v_max = max(v_data)
iq_min = min(iq_data)
iq_max = max(iq_data)

# plot iq-data
x=u_data
y=v_data

fig1, ax = plt.subplots()

# figure style
ax.scatter(x, y)

ax.set(xlim=(u_min, u_max),
       ylim=(v_min, v_max))

ax.set_xlabel("I")
ax.set_ylabel("Q")
ax.set_title("ResonatorSpectroscopy-experiment")
plt.show()

# measurement_frequency sweep & normalization of the signal
y_data_0 = iq_data

x_data = []
for i in range(len(exp_data.data())):
    x_data.append(data_raw[i]["metadata"]["xval"]) # the unit of x_data is "Hz"   

x_min = x_data[0]
x_max = x_data[-1]
y_min_0 = iq_min
y_max_0 = iq_max

y_data=[]
for i in range(len(y_data_0)):   
    y_data.append(y_data_0[i] / y_max_0) # y_data is normalized
    
y_min = y_min_0 / y_max_0
y_max = 1.
print((x_data))
print((y_data))

# raw data plot               
x=x_data
y=y_data

fig1, ax = plt.subplots()

# figure style
ax.scatter(x, y)

ax.set(xlim=(x_min, x_max),
       ylim=(y_min, y_max))

ax.set_xlabel("Measurement_frequency(Hz)")
ax.set_ylabel("Signal(normalized)")
ax.set_title("ResonatorSpectroscopy")

plt.show()               
```



![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/3e85b1e6-e2eb-4ba7-abd4-d1bf2ae680c6)





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/0ad3f888-fade-4c04-90f6-a0968c7d1eef)






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/3a329447-08b6-4af0-8881-9e6c6dee7829)





### Least-Square Fitting (ResonatorSpectroscopy)
```
import numpy as np
import matplotlib.pyplot as plt
import scipy 
from scipy.optimize import least_squares

# fit model
def func(x, u, y):
    return x[0] * x[1] / np.sqrt( x[1]**2 + 4 * (u - x[2])**2 ) + x[3]  - y

# experiment-raw-data 
u_data = np.array([6753294640.0, 6753494640.0, 6753694640.0, 6753894640.0, 6754094640.0, 6754294640.0, 6754494640.0, 6754694640.0, 6754894640.0, 6755094640.0, 6755294640.0, 6755494640.0, 6755694640.0, 6755894640.0, 6756094640.0, 6756294640.0, 6756494640.0, 6756694640.0, 6756894640.0, 6757094640.0, 6757294640.0, 6757494640.0, 6757694640.0, 6757894640.0, 6758094640.0, 6758294640.0, 6758494640.0, 6758694640.0, 6758894640.0, 6759094640.0, 6759294640.0, 6759494640.0, 6759694640.0, 6759894640.0, 6760094640.0, 6760294640.0, 6760494640.0, 6760694640.0, 6760894640.0, 6761094640.0, 6761294640.0, 6761494640.0, 6761694640.0, 6761894640.0, 6762094640.0, 6762294640.0, 6762494640.0, 6762694640.0, 6762894640.0, 6763094640.0, 6763294640.0, 6763494640.0, 6763694640.0, 6763894640.0, 6764094640.0, 6764294640.0, 6764494640.0, 6764694640.0, 6764894640.0, 6765094640.0, 6765294640.0, 6765494640.0, 6765694640.0, 6765894640.0, 6766094640.0, 6766294640.0, 6766494640.0, 6766694640.0, 6766894640.0, 6767094640.0, 6767294640.0, 6767494640.0, 6767694640.0, 6767894640.0, 6768094640.0, 6768294640.0, 6768494640.0, 6768694640.0, 6768894640.0, 6769094640.0, 6769294640.0, 6769494640.0, 6769694640.0, 6769894640.0, 6770094640.0, 6770294640.0, 6770494640.0, 6770694640.0, 6770894640.0, 6771094640.0, 6771294640.0, 6771494640.0, 6771694640.0, 6771894640.0, 6772094640.0, 6772294640.0, 6772494640.0, 6772694640.0, 6772894640.0, 6773094640.0, 6773294640.0, 6773494640.0, 6773694640.0, 6773894640.0, 6774094640.0, 6774294640.0, 6774494640.0, 6774694640.0, 6774894640.0, 6775094640.0, 6775294640.0, 6775494640.0, 6775694640.0, 6775894640.0, 6776094640.0, 6776294640.0, 6776494640.0, 6776694640.0, 6776894640.0, 6777094640.0, 6777294640.0, 6777494640.0, 6777694640.0, 6777894640.0, 6778094640.0, 6778294640.0, 6778494640.0, 6778694640.0, 6778894640.0, 6779094640.0, 6779294640.0, 6779494640.0, 6779694640.0, 6779894640.0, 6780094640.0, 6780294640.0, 6780494640.0, 6780694640.0, 6780894640.0, 6781094640.0, 6781294640.0, 6781494640.0, 6781694640.0, 6781894640.0, 6782094640.0, 6782294640.0, 6782494640.0, 6782694640.0, 6782894640.0, 6783094640.0, 6783294640.0, 6783494640.0, 6783694640.0, 6783894640.0, 6784094640.0, 6784294640.0, 6784494640.0, 6784694640.0, 6784894640.0, 6785094640.0, 6785294640.0, 6785494640.0, 6785694640.0, 6785894640.0, 6786094640.0, 6786294640.0, 6786494640.0, 6786694640.0, 6786894640.0, 6787094640.0, 6787294640.0, 6787494640.0, 6787694640.0, 6787894640.0, 6788094640.0, 6788294640.0, 6788494640.0, 6788694640.0, 6788894640.0, 6789094640.0, 6789294640.0, 6789494640.0, 6789694640.0, 6789894640.0, 6790094640.0, 6790294640.0, 6790494640.0, 6790694640.0, 6790894640.0, 6791094640.0, 6791294640.0, 6791494640.0, 6791694640.0, 6791894640.0, 6792094640.0, 6792294640.0, 6792494640.0, 6792694640.0, 6792894640.0, 6793094640.0, 6793294640.0])

# y_data is normalized!
y_data = np.array([0.015031612287449744, 0.007850190053852514, 0.01578013515646695, 0.020638609747596376, 0.01797401105288801, 0.01739105913595766, 0.018325115204466697, 0.02077146793553975, 0.020957943584871984, 0.01890088495296437, 0.024234416539410764, 0.02203643182891333, 0.010606995359381203, 0.013897771525098591, 0.01554358887224165, 0.012573595901032195, 0.0265047224862098, 0.021445539790744796, 0.021886692698987523, 0.03062977934895341, 0.03147209506504048, 0.02792318265285675, 0.01994319717488618, 0.019453918004763596, 0.02705760043351085, 0.020016069559827324, 0.02010294610568976, 0.023785162176880318, 0.034369405280656996, 0.03325252406329337, 0.0225569325395773, 0.030236870102772546, 0.030974154714281714, 0.03321889113035719, 0.02731175991726299, 0.029750444507938806, 0.031923750363285715, 0.035507079364015245, 0.03042273153767563, 0.03983826828730222, 0.03258931211950613, 0.03835105853810453, 0.035777720170415236, 0.030663676197789515, 0.02969489457248923, 0.031289649886626934, 0.027578871577880665, 0.03686404064364503, 0.033128406458627586, 0.03939083218355516, 0.0519737581328857, 0.04732103101336375, 0.05460986099005271, 0.05215448309584393, 0.0451303014447255, 0.04394754899345591, 0.04766300889901521, 0.04913924511180058, 0.04162988913065101, 0.049805384988060517, 0.04326731581602024, 0.054714072468368735, 0.0625325570364773, 0.061583489431451574, 0.05539151433327489, 0.0559619523274527, 0.05083205122240379, 0.05120331607506722, 0.059963714151676575, 0.05608384670392865, 0.06607030615451562, 0.0798221168672743, 0.07127372696624856, 0.08457739920233863, 0.09099586872771559, 0.0796090918373421, 0.07049550610975995, 0.07946816987988885, 0.08680037049423295, 0.10363449565818654, 0.11725045543834514, 0.1390598394754726, 0.1427702783695797, 0.15921698857052108, 0.15359227892882915, 0.1443188556095092, 0.11701630120737809, 0.07004655413814259, 0.0703872021691339, 0.11172948031941622, 0.19220113651315765, 0.2936102830146666, 0.39359253909983105, 0.5053528756782825, 0.6171920263253775, 0.7168753943740281, 0.7904404631425734, 0.8880650245741435, 0.9412781019307219, 0.973133056263582, 1.0, 0.9978115230653987, 0.9788658898630902, 0.9083395289697155, 0.8626754004600913, 0.7725910178352495, 0.7012718010502006, 0.5821341438088002, 0.47047140233989143, 0.3468612743330945, 0.2451144359757908, 0.14208567359187724, 0.06350889969907646, 0.039267752952037015, 0.08969571455002244, 0.14277008694088086, 0.16190761123361375, 0.17708268584508546, 0.1723915742876866, 0.16417597968214365, 0.14258431232277607, 0.10449346070738541, 0.07610679187172081, 0.04332475457478579, 0.044604784188999276, 0.04740660864884768, 0.07599233163463098, 0.09525650221822318, 0.09648534637872111, 0.09897012851594464, 0.0897675977613828, 0.07579092140030934, 0.054958493071441114, 0.03835031045179723, 0.033511981371702644, 0.031011716165730317, 0.03692867004687742, 0.0565097637549193, 0.06176966870285893, 0.06205116419369078, 0.06840725163012411, 0.06308955626103373, 0.05419390466444331, 0.04167369463576492, 0.04042279520110282, 0.024360463566358397, 0.03239694335485695, 0.03884105624804128, 0.03722868168416032, 0.05039795621841155, 0.046617687008251585, 0.05521129492336528, 0.0529087112542944, 0.039128224956487774, 0.04166583264493427, 0.03259422170723512, 0.02774250760191601, 0.015404340738052604, 0.02734374909240334, 0.03387593848405631, 0.04161238638298595, 0.03742492683060842, 0.042209569191881714, 0.02967180545735793, 0.02879252955232529, 0.02385457223137101, 0.023003479004888097, 0.014412153269855888, 0.02752458572772621, 0.022446012422465512, 0.02758635977122928, 0.03772013845290752, 0.03406887835712418, 0.04030888767685117, 0.02929273814154161, 0.027257518453455806, 0.022803384956276103, 0.014098622584243426, 0.015013045473945835, 0.015823696420871416, 0.02337244692133532, 0.02758645218455512, 0.03546811456496826, 0.024888065889012725, 0.03323561287387651, 0.028782493772367444, 0.02582026046126815, 0.01884951364957281, 0.017523000686121427, 0.013367126194956978, 0.012704933303226393, 0.017432501715140526, 0.016780418717658025, 0.01646127172344991, 0.03056804449309004, 0.022168784671951637, 0.0251321636475501, 0.015053361350461045, 0.01789265343478121, 0.011294276552260328, 0.014165840038310607])
# initial guess of fitting parameters
x0 = np.array([1.00, 10.0e6, 6.77e9, 1.00])

# main
res = least_squares(fun=func, x0=x0, args=(u_data, y_data), verbose=2)
print(res.x)

measurement_frequency = res.x[2]
kappa = res.x[1]

# a solution with least-squares method
u_min = u_data[0]
u_max = u_data[-1]
u_val = np.linspace(u_min, u_max, 1001)
y_val = res.x[0] * res.x[1] / np.sqrt( res.x[1]**2 + 4 * (u_val - res.x[2])**2 ) + res.x[3] 

print(f"The value of measurement_frequency is {measurement_frequency} (Hz).")
print(f"The value of kappa is {kappa} (Hz).")

# figure
plt.plot(u_data, y_data, "ob", label = "data")
plt.plot(u_val, y_val, "-r", label = "least_square")

plt.xlabel("meas_frequency(Hz)")
plt.ylabel("Signal")
plt.title("ResonatorSpectroscopy-experiment")
plt.legend()
plt.show()
```

The value of measurement_frequency is 6773361389.848444 (Hz).
The value of kappa is 1314190.825699872 (Hz).





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/f325a489-4f70-41b5-9c36-67e476528db3)






### Issue#1238(also #1221)

### reno

features-expclass:
  - |
    Added minimal working code examples to the API pages for each
    experiment, especially in characterizaion experiments.
    The minimal working code example is a code snippet which users can copy
    and paste to run the experiment. Users are required to specify a backend
    in the code example. As a default, the backend is a simulator such as
    `GenericBackendV2(num_qubits=27)`. In some experiments,
    the backend is an IBM real device due to the `backend.defaults()` dependence
    of the code example.

### Pull request checklist (for qiskit)

1. The code follows the code style of the project and successfully
   passes the tests. For convenience, you can execute `tox` locally,
   which will run these checks and report any issues.
   If your code fails the local style checks (specifically the black
   code formatting check) you can use `tox -eblack` to automatically
   fix update the code formatting.


2. The documentation has been updated accordingly. In particular, if a
   function or class has been modified during the PR, please update the
   *docstring* accordingly.

   If your pull request is adding a new class, function, or module that is
   intended to be user facing ensure that you've also added those to a
   documentation `autosummary` index to include it in the api documentation.
   For more details you can refer to:

   https://qiskit.org/documentation/contributing_to_qiskit.html#documentation-structure


3. If it makes sense for your change that you have added new tests that
   cover the changes.


4. Ensure that if your change has an end user facing impact (new feature,
   deprecation, removal etc) that you have added a reno release note for that
   change and that the PR is tagged for the changelog.


### Pull request checklist (for qiskit-experiments)

When submitting a pull request for review, please ensure that:

1. The code follows the code style of the project and successfully passes the tests.

tox -epy310                      : well-done

tox -elint & tox -eblack         : well-done

tox -e docs                      : well-done

2. The API documentation has been updated accordingly.

yes

3. You have updated the relevant documentation or written new docs. In case the PR needs
   to be merged without delay (e.g. for a high priority fix), open an issue for updating
   or adding the documentation later.

yes

4. You've added tests that cover the changes you've made, if relevant.

I've not added any tests. This is no problem.

5. If your change has an end user facing impact (new feature, deprecation, removal,
   etc.), you've added or updated a reno release note for that change and tagged the PR
   for the changelog.

I've added a reno release notes which describes the new feature.

6. If your code requires a change to dependencies, you've updated the corresponding
   requirements file: `requirements.txt` contain core dependencies,
   `requirements-extras.txt` for dependencies for optional features, and `requirements-dev.txt`
   for dependencies required for running tests and building documentation.

My code does not require any changes to dependencies. This is no problem.

### PR for Qiskit-Extesions/qiskit-experiments issue#1238

PR title : Add minimal working code expample in experiment API docs


### Summary

This change adds minimal working code examples to the API pages for each experiment,especially in characterizaion experiments. The minimal working code example is a code snippet which users can copy and paste to run the experiment. 

### Details and comments

- This change solves the `issue#1238` (See also the `issue#1221`).
- In the API pages, the documentation for each experiment has been updated with its code example. Users are required to specify a backend in the code example. As a default, the backend is a simulator such as `GenericBackendV2(num_qubits=27)`. 
- In some experiments,
`CorrelatedReadoutError` 
`ResonatorSpectroscopy` 
`CrossResonanceHamiltonian` and 
`EchoedCrossResonanceHamiltonian`, 
the backend is an IBM Quantum real device due to the `backend.defaults()` dependence of the code example.
In these cases, the code example explicitly imports the module `qiskit-ibm-provider` in order to retrieve the job on IBM Quantum real devices.
Therefore, `requirements-dev.txt` has been updated to add the dependency on `qiskit-ibm-provider` for running tests and building documentation.  


### PR checklist (template)

- [x] I have read the contributing guide `CONTRIBUTING.md`.
- [ ] I have added the tests to cover my changes.
- [x] I have updated the documentation accordingly.
- [x] I have added a release note file using `reno` if this change needs to be documented in the release notes.


### PR checklist (additional in CONTRIBUTING.md)

- [x] The code follows the code style of the project and successfully passes the tests.
- [x] The API documentation has been updated accordingly.
- [x] You have updated the relevant documentation or written new docs. In case the PR needs
      to be merged without delay (e.g. for a high priority fix), open an issue for updating
      or adding the documentation later.
- [] You've added tests that cover the changes you've made, if relevant.
- [] If your change has an end user facing impact (new feature, deprecation, removal,
     etc.), you've added or updated a reno release note for that change and tagged the PR
     for the changelog.
- [x] If your code requires a change to dependencies, you've updated the corresponding
      requirements file: `requirements.txt` for core dependencies,
      `requirements-extras.txt` for dependencies for optional features, and `requirements-dev.txt`
      for dependencies required for running tests and building documentation.




### BackebndData(backned=MockIQBackend())
```
# backend MockIQBackend
# Note num_qubits=2 !
# How to use BackendData(backend)?

from qiskit_experiments.framework import BackendData
from qiskit_experiments.test.mock_iq_backend import MockIQBackend

backend = MockIQBackend()
backend_data = BackendData(backend)

print()

print(dir(backend_data))

print()

print(backend_data.__dict__.keys())

print()

print(backend_data._backend.__dict__.keys())

print()

print(backend_data._backend.__dict__.get("_defs_dict").keys())

print()

print(backend_data._backend.__dict__.get("_defs_dict").get("meas_freq_est"))

print()

print(backend_data._pulse_conf.__dict__.keys())

print()

print(backend_data._pulse_conf.__dict__.get("qubit_lo_range"))

print()

print(backend_data._pulse_conf.__dict__.get("meas_lo_range"))

print()

print(backend_data.acquire_alignment)

print()

print(backend.acquire_channel(qubit=1))

print()

print(backend_data.control_channel(qubits=(0, 1)))

print()

print(backend_data.drive_channel(qubit=1))

print()

print(backend_data.drive_freqs)

print()

print(backend.dt)

print()

print(backend_data.granularity)

print()

print(backend_data.meas_freqs)

print()

print(dir(backend_data.meas_freqs))

print()

print(backend_data.measure_channel(qubit=1))

print()

print(backend_data.min_length)

print()

print(backend_data.name)

print()

print(backend_data.num_qubits)

print()

print(backend_data.pulse_alignment)

print()

print(backend_data.qubit_t1(qubit=1))

print()

print(backend_data.version)
```

['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', '_backend', '_parse_additional_data', '_pulse_conf', '_v1', '_v2', 'acquire_alignment', 'acquire_channel', 'control_channel', 'coupling_map', 'drive_channel', 'drive_freqs', 'dt', 'granularity', 'is_simulator', 'max_circuits', 'meas_freqs', 'measure_channel', 'min_length', 'name', 'num_qubits', 'provider', 'pulse_alignment', 'qubit_t1', 'version']

dict_keys(['_backend', '_v1', '_v2', '_pulse_conf'])

dict_keys(['_experiment_helper', '_rng', '_conf_dict_real', '_props_dict', '_defs_dict', '_options', '_provider', 'name', 'description', 'online_date', 'backend_version', '_coupling_map', '_target', 'sim', 'channels_map', '_defaults'])

dict_keys(['qubit_freq_est', 'meas_freq_est', 'buffer', 'pulse_library', 'cmd_def', '_data'])

[6.5, 6.6000000000000005]

dict_keys(['n_uchannels', 'u_channel_lo', 'meas_levels', 'qubit_lo_range', 'meas_lo_range', 'meas_kernels', 'discriminators', 'hamiltonian', 'rep_times', 'dt', 'dtm', 'channels', '_qubit_channel_map', '_channel_qubit_map', '_control_channels', 'channel_bandwidth', 'acquisition_latency', 'conditional_latency', 'meas_map', '_data', 'backend_name', 'backend_version', 'n_qubits', 'basis_gates', 'gates', 'local', 'simulator', 'conditional', 'open_pulse', 'memory', 'max_shots', 'coupling_map', 'dynamic_reprate_enabled', 'n_registers', 'description', 'processor_type'])

[[4500000000.0, 5500000000.0], [4500000000.0, 5500000000.0]]

[[6000000000.0, 7000000000.0], [6000000000.0, 7000000000.0]]

1

AcquireChannel(1)

[ControlChannel(0)]

DriveChannel(1)

[4919968006.92, 5019968006.92]

1.3333e-09

1

[]

['__add__', '__class__', '__class_getitem__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']

MeasureChannel(1)

1

fake_openpulse_2q

2

1

8.19500421005539e-05

2
