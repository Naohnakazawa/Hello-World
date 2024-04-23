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








### Plot Experiment Raw Data (QubitSpectroscopy)
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
from qiskit_experiments.library.characterization import QubitSpectroscopy

qubit = 107
qubit_freq_estimate = backend.defaults().qubit_freq_est[qubit]
frequencies=np.linspace(qubit_freq_estimate - 20.0e6, qubit_freq_estimate + 20.0e6, 201)
exp = QubitSpectroscopy(physical_qubits = (qubit,),
                        backend = backend,
                        frequencies=frequencies,
                        absolute = True,
                        )
exp.set_run_options(shots=1000)

print(exp.circuits()[0])
print(backend.defaults().qubit_freq_est[qubit])

step1=False  # "False" shows the result in the previous run
if step1==True:
    exp_data = exp.run().block_for_results()
    
else:
    # retrieve your jobs
    from qiskit_experiments.framework import ExperimentData
    job_ids = ["crfqpd1d7s5g008xx26g"] # case: backend=ibm_kyoto, shots=1000, qubits=(107,),
                                       # frequencies=np.linspace(qubit_freq_estimate - 20.0e6, qubit_freq_estimate + 20.0e6, 201)       
    exp_data = ExperimentData(experiment=exp)
    exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
    exp.analysis.run(exp_data)
    exp_data.block_for_results()
    
result = exp_data.analysis_results()
exp_data.figure(0)

# plot iq-data
data_raw = exp_data.data()

iq_data=[]
u_data=[]
v_data=[]

for i in range(len(exp_data.data())):    
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

# data
x=u_data
y=v_data

fig1, ax = plt.subplots()

# figure style
ax.scatter(x, y)

ax.set(xlim=(u_min, u_max),
       ylim=(v_min, v_max))

ax.set_xlabel("I")
ax.set_ylabel("Q")
ax.set_title("QubitSpectroscopy-experiment")
plt.show()

# plot qubit-frequency sweep & normalized signal
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

# data
x=x_data
y=y_data

fig1, ax = plt.subplots()

# figure style
ax.scatter(x, y)

ax.set(xlim=(x_min, x_max),
       ylim=(y_min, y_max))

ax.set_xlabel("Drive_frequency(Hz)")
ax.set_ylabel("Signal")
ax.set_title("QubitrSpectroscopy")

plt.show()
```






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/a3bb1f45-a436-433f-8aad-ab118fca0da6)






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/7bd8d8eb-4810-447f-87d0-4fad1ed0500e)








![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/d121b864-9223-4ff9-a38b-b450324621c5)







### Least-Square Fitting (QubitSpectroscopy)
```
import numpy as np
import matplotlib.pyplot as plt
import scipy 
from scipy.optimize import least_squares

# fit model
def func(x, u, y):
    return x[0] * x[1] / np.sqrt( x[1]**2 + 4 * (u - x[2])**2 ) + x[3]  - y

# experiment-raw-data 
u_data = np.array([5000643667.991, 5000843667.991, 5001043667.991, 5001243667.991, 5001443667.991, 5001643667.991, 5001843667.991, 5002043667.991, 5002243667.991, 5002443667.991, 5002643667.991, 5002843667.991, 5003043667.991, 5003243667.991, 5003443667.991, 5003643667.991, 5003843667.991, 5004043667.991, 5004243667.991, 5004443667.991, 5004643667.991, 5004843667.991, 5005043667.991, 5005243667.991, 5005443667.991, 5005643667.991, 5005843667.991, 5006043667.991, 5006243667.991, 5006443667.991, 5006643667.991, 5006843667.991, 5007043667.991, 5007243667.991, 5007443667.991, 5007643667.991, 5007843667.991, 5008043667.991, 5008243667.991, 5008443667.991, 5008643667.991, 5008843667.991, 5009043667.991, 5009243667.991, 5009443667.991, 5009643667.991, 5009843667.991, 5010043667.991, 5010243667.991, 5010443667.991, 5010643667.991, 5010843667.991, 5011043667.991, 5011243667.991, 5011443667.991, 5011643667.991, 5011843667.991, 5012043667.991, 5012243667.991, 5012443667.991, 5012643667.991, 5012843667.991, 5013043667.991, 5013243667.991, 5013443667.991, 5013643667.991, 5013843667.991, 5014043667.991, 5014243667.991, 5014443667.991, 5014643667.991, 5014843667.991, 5015043667.991, 5015243667.991, 5015443667.991, 5015643667.991, 5015843667.991, 5016043667.991, 5016243667.991, 5016443667.991, 5016643667.991, 5016843667.991, 5017043667.991, 5017243667.991, 5017443667.991, 5017643667.991, 5017843667.991, 5018043667.991, 5018243667.991, 5018443667.991, 5018643667.991, 5018843667.991, 5019043667.991, 5019243667.991, 5019443667.991, 5019643667.991, 5019843667.991, 5020043667.991, 5020243667.991, 5020443667.991, 5020643667.991, 5020843667.991, 5021043667.991, 5021243667.991, 5021443667.991, 5021643667.991, 5021843667.991, 5022043667.991, 5022243667.991, 5022443667.991, 5022643667.991, 5022843667.991, 5023043667.991, 5023243667.991, 5023443667.991, 5023643667.991, 5023843667.991, 5024043667.991, 5024243667.991, 5024443667.991, 5024643667.991, 5024843667.991, 5025043667.991, 5025243667.991, 5025443667.991, 5025643667.991, 5025843667.991, 5026043667.991, 5026243667.991, 5026443667.991, 5026643667.991, 5026843667.991, 5027043667.991, 5027243667.991, 5027443667.991, 5027643667.991, 5027843667.991, 5028043667.991, 5028243667.991, 5028443667.991, 5028643667.991, 5028843667.991, 5029043667.991, 5029243667.991, 5029443667.991, 5029643667.991, 5029843667.991, 5030043667.991, 5030243667.991, 5030443667.991, 5030643667.991, 5030843667.991, 5031043667.991, 5031243667.991, 5031443667.991, 5031643667.991, 5031843667.991, 5032043667.991, 5032243667.991, 5032443667.991, 5032643667.991, 5032843667.991, 5033043667.991, 5033243667.991, 5033443667.991, 5033643667.991, 5033843667.991, 5034043667.991, 5034243667.991, 5034443667.991, 5034643667.991, 5034843667.991, 5035043667.991, 5035243667.991, 5035443667.991, 5035643667.991, 5035843667.991, 5036043667.991, 5036243667.991, 5036443667.991, 5036643667.991, 5036843667.991, 5037043667.991, 5037243667.991, 5037443667.991, 5037643667.991, 5037843667.991, 5038043667.991, 5038243667.991, 5038443667.991, 5038643667.991, 5038843667.991, 5039043667.991, 5039243667.991, 5039443667.991, 5039643667.991, 5039843667.991, 5040043667.991, 5040243667.991, 5040443667.991, 5040643667.991])

# y_data is normalized
y_data = np.array([0.9687525924173334, 0.9752126512627716, 0.978908669433078, 0.9780590951341485, 0.9580033620200062, 0.9728942899885004, 0.9747368602445904, 0.9628137680897693, 0.9619248683626866, 0.9729121610331241, 0.972028900018207, 0.9640725224095408, 0.9918255042067136, 0.961107543451615, 0.9829016389569908, 0.9744866112739535, 0.958219231773518, 0.966739706660025, 0.9488684611285584, 0.9773067732513036, 0.9708310312480763, 0.95808573117867, 0.9759031495832883, 0.9751296312868917, 0.9760934024700986, 0.9618063018178363, 0.9652501537272592, 0.962356170176169, 0.9733401550906452, 0.9797123436068694, 0.980431336951669, 0.9849405188793778, 0.9694845518648643, 0.9858184770749356, 0.9699211883894373, 0.9786623670405279, 0.9652471581197346, 0.9613642662559552, 0.9538698355532195, 0.9556914119144316, 0.9717178277575544, 0.9636606945898205, 0.9697982921095935, 0.9740523119721989, 0.9933029975051657, 0.9825612974095849, 0.9593773828306208, 0.9643452727098745, 0.9631704915729326, 0.9737509425929198, 0.9668391033826917, 0.973317318464435, 0.9595484107270337, 0.9804001722843039, 0.9823617660083289, 0.9802580616023695, 0.9720898180149108, 0.9655707894829054, 0.967814667026722, 0.9646959100566211, 0.9595218960914806, 0.9661502647183656, 0.9596271180696353, 0.9602164527845514, 0.943224536340614, 0.9532735038973169, 0.9541478286189594, 0.9446278994612124, 0.9626592369728326, 0.9422882064226095, 0.9209759569489081, 0.9313497327830099, 0.9263965002846652, 0.9176829219568269, 0.9211361786675415, 0.9223319014107649, 0.9007878222107845, 0.9003943930650332, 0.9026220576811417, 0.9004921017231028, 0.9037555617993063, 0.8968407422219566, 0.9087938108688205, 0.890142065028645, 0.9084941697090712, 0.9150988461767448, 0.8954106555479503, 0.901473475579529, 0.8929419317118376, 0.903450310674024, 0.9158417122733277, 0.9073616961749099, 0.9027163154373832, 0.9043806867385324, 0.897782934887271, 0.9098673499481615, 0.9132443812366433, 0.8921876516774045, 0.907373490916189, 0.8940500426357684, 0.8991666497730103, 0.9013395795364454, 0.8954966844516805, 0.8898331328942528, 0.9078052647998455, 0.8984268519995723, 0.8841148651562805, 0.9004307681846051, 0.8995198154104149, 0.8954323874264797, 0.8954866356594502, 0.8950631884644016, 0.906586290989708, 0.9042427381429207, 0.9011117259278931, 0.8994924576613746, 0.8899518905859554, 0.8837888895263641, 0.9000878014255385, 0.8869417054678799, 0.8969587017181477, 0.9033446831474837, 0.9107348640365832, 0.9176843395781561, 0.9138992764975041, 0.9150763076273549, 0.902845220098661, 0.9168558210548805, 0.9171335504516661, 0.9203138864231019, 0.9142934239668075, 0.9203289121313589, 0.9295366319136052, 0.9407971670740107, 0.9438125701264104, 0.9426832079060714, 0.9388200599494211, 0.9522165997127962, 0.970145448304342, 0.964120964957367, 0.959817422293161, 0.9508534251902965, 0.9431908396388601, 0.9590472284649361, 0.9744550199295935, 1.0, 0.9609767769197514, 0.9691597574711296, 0.9708059059703424, 0.9784910379117233, 0.9728388081146147, 0.9728059869616776, 0.9703226546003589, 0.9787686943670163, 0.9605645217364145, 0.9759023990309889, 0.9863181173915474, 0.9706194562202476, 0.9576714384554673, 0.9711528174755206, 0.9607794877355211, 0.9765427596449632, 0.9627975737134022, 0.9619914954785654, 0.9608656117630658, 0.9772761548908241, 0.9792746035169072, 0.9684970028685231, 0.9576402349153049, 0.9656974270957588, 0.9796900969152492, 0.9633488969364825, 0.968298920725273, 0.9670967978506609, 0.9645707344604135, 0.9722376117682697, 0.9697192392669847, 0.9657311647086753, 0.9602297217936193, 0.9616930903477671, 0.951534374891786, 0.9646736487155834, 0.9701860777904776, 0.9746428281812249, 0.965534140345902, 0.9744556210358586, 0.9769112019480932, 0.9531276079241741, 0.9574641536382958, 0.9608855492993338, 0.9683053336513765, 0.9709568048090533, 0.9805897510998468, 0.9772190891919792, 0.9706914582148597, 0.9531380117179636, 0.9638570790441415, 0.9672396118703068, 0.9721103242648819, 0.967205245362132, 0.9609150220313566])
# initial guess of fitting parameters
x0 = np.array([-1.00, 20.0e6, 5.03e9, 1.00])

# main
res = least_squares(fun=func, x0=x0, args=(u_data, y_data), verbose=2)
print(res.x)

qubit_frequency = res.x[2]
kappa = res.x[1]

# a solution with least-squares method
u_min = u_data[0]
u_max = u_data[-1]
u_val = np.linspace(u_min, u_max, 1001)
y_val = res.x[0] * res.x[1] / np.sqrt( res.x[1]**2 + 4 * (u_val - res.x[2])**2 ) + res.x[3] 

print(f"The value of qubit_frequency is {qubit_frequency} (Hz).")
print(f"The value of kappa is {kappa} (Hz).")

# figure
plt.plot(u_data, y_data, "ob", label = "data")
plt.plot(u_val, y_val, "-r", label = "least_square")

plt.xlabel("qubit_frequency(Hz)")
plt.ylabel("Signal")
plt.title("QubitSpectroscopy-experiment")
plt.legend()
plt.show()
```

The value of qubit_frequency is 5021053323.792102 (Hz).
The value of kappa is 8734845.157049231 (Hz).







![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/bd2f6399-de90-4d92-85be-3af0242691ed)







### Plot Experiment Raw Data (MultiStateDiscrimination)
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm-kyoto
from qiskit_ibm_provider import IBMProvider
provider = IBMProvider() 
backend = provider.get_backend("ibm_sherbrooke")

# ..jupyter-execute::

# mwc
import numpy as np
import matplotlib.pyplot as plt
from qiskit_experiments.library.characterization import MultiStateDiscrimination

qubit=107
exp=MultiStateDiscrimination([qubit], backend=backend)
exp.set_run_options(shots=100)

step1=False  # "False" shows the result in the previous run
if step1==True:
    exp_data = exp.run().block_for_results()
    
else:
    # retrieve your jobs
    from qiskit_experiments.framework import ExperimentData
    job_ids = ["crgmfvzqzd5000886y7g"] # case: backend=ibm_sherbrooke, shots=100, physical_qubits=[107]
                                              
    exp_data = ExperimentData(experiment=exp)
    exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
    exp.analysis.run(exp_data)
    exp_data.block_for_results()
    
result = exp_data.analysis_results()
exp_data.figure(0)

# ..jupyter-execute::

# plot iq-data (iq_center with standard_deviation)
from matplotlib.patches import Ellipse

data_raw = exp_data.data()
print(f"The qubit state is {data_raw[0].get('metadata')} for qubit=107.")

iq_center=[]
u_data=[]
v_data=[]

for i in range(len(data_raw[0].get("memory"))):    
    u, v=data_raw[0].get("memory")[i][0]    
    u_data.append(u)       
    v_data.append(v)
    
u_min = min(u_data)
u_max = max(u_data)
v_min = min(v_data)
v_max = max(v_data)

iq_center = [np.mean(u_data), np.mean(v_data)]
iq_std = [np.std(u_data), np.std(v_data)]

print(data_raw[0].get("metadata"))
print(iq_center)
print(iq_std)

# data
x=u_data
y=v_data

fig0, ax = plt.subplots()

# figure style
ax.scatter(x, y, marker="o", color="b")

# The ellipse of 100 shots
width = 2 * iq_std[0]
height = 2 * iq_std[1]
ellipse = Ellipse(xy=(iq_center[0], iq_center[1]),
                  width = width,
                  height = height,
                  facecolor="none",
                  edgecolor="b",)

ax.add_patch(ellipse)

ax.set(xlim=(u_min, u_max),
       ylim=(v_min, v_max))

ax.set_xlabel("I")
ax.set_ylabel("Q")
ax.set_title("MultiStateDiscrimination '0'")
plt.show()

# ..jupyter-execute::

print(f"The qubit state is {data_raw[1].get('metadata')} for qubit=107.")

iq_center=[]
u_data=[]
v_data=[]

for i in range(len(data_raw[1].get("memory"))):    
    u, v=data_raw[1].get("memory")[i][0]    
    u_data.append(u)       
    v_data.append(v)
    
u_min = min(u_data)
u_max = max(u_data)
v_min = min(v_data)
v_max = max(v_data)

iq_center = [np.mean(u_data), np.mean(v_data)]
iq_std = [np.std(u_data), np.std(v_data)]

print(data_raw[1].get("metadata"))
print(iq_center)
print(iq_std)

# data
x=u_data
y=v_data

fig1, ax = plt.subplots()

# figure style
ax.scatter(x, y, marker="o", color="r")

# The ellipse of 100 shots
width = 2 * iq_std[0]
height = 2 * iq_std[1]
ellipse = Ellipse(xy=(iq_center[0], iq_center[1]),
                  width = width,
                  height = height,
                  facecolor="none",
                  edgecolor="r",)

ax.add_patch(ellipse)

ax.set(xlim=(u_min, u_max),
       ylim=(v_min, v_max))

ax.set_xlabel("I")
ax.set_ylabel("Q")
ax.set_title("MultiStateDiscrimination '0'")
plt.show()

```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/4881cd64-232c-42b1-ab31-45fb6fc9634b)







The qubit state is {'label': 0} for qubit=107.
{'label': 0}
[-72705130.43, 83287911.52]
[22681331.42185364, 13971081.881666945]





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/703dcf6f-2789-4a99-9611-af9c943c9e31)







The qubit state is {'label': 1} for qubit=107.
{'label': 1}
[38391372.06, 85121201.36]
[16765032.499758402, 12909267.318687618]







![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/093ffb8d-2d96-4c47-b102-db20bcbc6531)







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
