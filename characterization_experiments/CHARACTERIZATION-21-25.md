# Characterization experiments 21, 22, 23, 24, 25
### CRH
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm_kyoto
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_kyoto")

# ..jupyter-execute::

from qiskit_experiments.library.characterization import CrossResonanceHamiltonian

qubits=(17, 30)
exp=CrossResonanceHamiltonian(physical_qubits=qubits, backend=backend)

exp.set_experiment_options(
                       min_durations=1.0e-07, 
                       max_durations=1.2e-06, 
                       num_durations=48, 
                       amp=0.2024212826036257, 
                       amp_t=0.0, 
                       sigma=30, 
                       risefall=2)

exp.set_run_options(shots=1000)
print(exp.circuits()[0])
print(exp.circuits()[287])

step1=False # run carefully if your device is a real device!
if step1==True:
    
    exp_data=exp.run().block_for_results()
    result=exp_data.analysis_results()
    
    for _ in result:
        print(_)
        
    exp_data.figure(0)

else:
    pass
    
# retrieve your jobs
from qiskit_experiments.framework import ExperimentData

#job_ids= ["cnvwmx38ybr000846y6g"] # case: shots=100

job_ids= ["cnvwv2c8ybr000846yz0"] # List of job IDs for the experiment
exp_data = ExperimentData(experiment=exp)
exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp.analysis.run(exp_data)
exp_data.block_for_results()
result=exp_data.analysis_results()

exp_data.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/a500c14d-0bc6-4f03-8761-e95a899a9835)








AnalysisResult
- name: @Parameters_ctrl0
- value: CurveFitResult:
 - fitting method: least_squares
 - number of sub-models: 3
  * F_x(x) = (-pz * px + pz * px * cos(sqrt(px**2 + py**2 + pz**2) * (x +...
  * F_y(x) = (pz * py - pz * py * cos(sqrt(px**2 + py**2 + pz**2) * (x + ...
  * F_z(x) = (pz**2 + (px**2 + py**2) * cos(sqrt(px**2 + py**2 + pz**2) *...
 - success: True
 - number of function evals: 74
 - degree of freedom: 139
 - chi-square: 124.6108206405537
 - reduced chi-square: 0.8964807240327605
 - Akaike info crit.: -10.824970971850568
 - Bayesian info crit.: 4.024095526029434
 - init params:
  * pz = 1877526.5222413656
  * px = -3386916.116137368
  * py = 4.147775980608213e-10
  * t_off = 3.7599424119465005e-08
  * b = 1e-09
 - fit params:
  * pz = 112458.05870040275 ± 8189.872675773713
  * px = -1298561.8347733433 ± 15151.577814682956
  * py = -1584307.3356464775 ± 13206.92515025882
  * t_off = 3.220323499022591e-08 ± 4.72298433125466e-09
  * b = -0.029669169063134233 ± 0.004220436834571001
 - correlations:
  * (py, b) = -0.5598871582607898
  * (pz, b) = -0.2724743478765941
  * (px, py) = -0.2654512906923372
  * (pz, px) = -0.08497588468419871
  * (pz, t_off) = -0.043652923333093924
  * (py, t_off) = 0.21464423898839766
  * (pz, py) = 0.31352277114014965
  * (t_off, b) = 0.5475033474596284
  * (px, b) = 0.7487750018644969
  * (px, t_off) = 0.7635900139738441
- quality: good
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False
AnalysisResult
- name: @Parameters_ctrl1
- value: CurveFitResult:
 - fitting method: least_squares
 - number of sub-models: 3
  * F_x(x) = (-pz * px + pz * px * cos(sqrt(px**2 + py**2 + pz**2) * (x +...
  * F_y(x) = (pz * py - pz * py * cos(sqrt(px**2 + py**2 + pz**2) * (x + ...
  * F_z(x) = (pz**2 + (px**2 + py**2) * cos(sqrt(px**2 + py**2 + pz**2) *...
 - success: True
 - number of function evals: 38
 - degree of freedom: 139
 - chi-square: 167.66461412842574
 - reduced chi-square: 1.2062202455282427
 - Akaike info crit.: 31.909936960277157
 - Bayesian info crit.: 46.75900345815716
 - init params:
  * pz = 1326792.5487957431
  * px = 5.313517671264104e-11
  * py = 867763.2889684736
  * t_off = 3.7599424119465005e-08
  * b = 1e-09
 - fit params:
  * pz = 59678.00502819103 ± 17020.906813180503
  * px = 402362.5434051458 ± 12588.204520692623
  * py = 1048000.5147936983 ± 11762.415683782156
  * t_off = 2.9179971784461202e-08 ± 7.618145838683671e-09
  * b = -0.0341121324986928 ± 0.002594095165189068
 - correlations:
  * (py, t_off) = -0.8842006903003622
  * (px, t_off) = -0.242247668470561
  * (py, b) = -0.16588744975351172
  * (pz, t_off) = -0.0458296219647343
  * (pz, py) = 0.16872912388067415
  * (t_off, b) = 0.22294389624153932
  * (px, py) = 0.27743614047445375
  * (pz, b) = 0.4646186119829769
  * (px, b) = 0.6251179807501309
  * (pz, px) = 0.6882667480857053
- quality: good
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False
AnalysisResult
- name: omega_zx
- value: (-1.354+/-0.016)e+05
- quality: good
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False
AnalysisResult
- name: omega_zy
- value: (-2.095+/-0.014)e+05
- quality: good
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False
AnalysisResult
- name: omega_zz
- value: (4.2+/-1.5)e+03
- quality: good
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False
AnalysisResult
- name: omega_ix
- value: (-7.13+/-0.16)e+04
- quality: good
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False
AnalysisResult
- name: omega_iy
- value: (-4.27+/-0.14)e+04
- quality: good
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False
AnalysisResult
- name: omega_iz
- value: (1.37+/-0.15)e+04
- quality: good
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False




### ECRH
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm-kyoto
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_kyoto")

# ..jupyter-execute::

from qiskit_experiments.library.characterization import EchoedCrossResonanceHamiltonian

qubits=(17, 30)
exp=EchoedCrossResonanceHamiltonian(physical_qubits=qubits, backend=backend)

exp.set_experiment_options(max_circuits=None, 
                       flat_top_widths=None, 
                       durations=None, 
                       min_durations=1.0e-07, 
                       max_durations=1.2e-06, 
                       num_durations=48, 
                       amp=0.2024212826036257, 
                       amp_t=0.0, 
                       sigma=30, 
                       risefall=2)

exp.set_run_options(shots=1000)

step1=False # run carefully if your device is a real device!
if step1==True:
    
    exp_data=exp.run().block_for_results()
    result=exp_data.analysis_results()

    for _ in result:
        print(_)
        
    exp_data.figure(0)

else:
    pass 

# retrieve your jobs
from qiskit_experiments.framework import ExperimentData 

# job_ids= ["cnw40cswsx00008zawy0"] # case : shots=100

job_ids= ["cnwbhqe5vh500087x5fg"] # case : shots=1000
exp_data = ExperimentData(experiment=exp)
exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp.analysis.run(exp_data)
exp_data.block_for_results()
result=exp_data.analysis_results()

exp_data.figure(0)
```






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/ae538ea7-06bc-44cd-a050-a9dc60498bf0)








AnalysisResult
- name: @Parameters_ctrl0
- value: CurveFitResult:
 - fitting method: least_squares
 - number of sub-models: 3
  * F_x(x) = (-pz * px + pz * px * cos(sqrt(px**2 + py**2 + pz**2) * (x +...
  * F_y(x) = (pz * py - pz * py * cos(sqrt(px**2 + py**2 + pz**2) * (x + ...
  * F_z(x) = (pz**2 + (px**2 + py**2) * cos(sqrt(px**2 + py**2 + pz**2) *...
 - success: True
 - number of function evals: 96
 - degree of freedom: 139
 - chi-square: 812.8682260489522
 - reduced chi-square: 5.847972849273038
 - Akaike info crit.: 259.22882270860805
 - Bayesian info crit.: 274.077889206488
 - init params:
  * pz = 1134433.3051928212
  * px = 2710587.146168095
  * py = 0.0
  * t_off = 0.0
  * b = 1e-09
 - fit params:
  * pz = 175800.7691165281 ± 4850.5392818329565
  * px = -522643.81880874326 ± 10931.18114642865
  * py = -1399892.129817127 ± 7088.837861108819
  * t_off = 1.0747352148254395e-07 ± 6.443568689131086e-09
  * b = -0.0003521379048500801 ± 0.003060677955822022
 - correlations:
  * (pz, b) = -0.4420773644711919
  * (pz, px) = -0.3702959370852147
  * (px, py) = -0.302771254354155
  * (py, b) = -0.20669042288670733
  * (pz, t_off) = -0.17691924394990707
  * (pz, py) = 0.3252682408376346
  * (px, t_off) = 0.47669745039837214
  * (t_off, b) = 0.4895880486168033
  * (py, t_off) = 0.576815343780813
  * (px, b) = 0.766032232480821
- quality: bad
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False
AnalysisResult
- name: @Parameters_ctrl1
- value: CurveFitResult:
 - fitting method: least_squares
 - number of sub-models: 3
  * F_x(x) = (-pz * px + pz * px * cos(sqrt(px**2 + py**2 + pz**2) * (x +...
  * F_y(x) = (pz * py - pz * py * cos(sqrt(px**2 + py**2 + pz**2) * (x + ...
  * F_z(x) = (pz**2 + (px**2 + py**2) * cos(sqrt(px**2 + py**2 + pz**2) *...
 - success: True
 - number of function evals: 69
 - degree of freedom: 139
 - chi-square: 666.4721768397674
 - reduced chi-square: 4.7947638621566
 - Akaike info crit.: 230.63465353524626
 - Bayesian info crit.: 245.48372003312625
 - init params:
  * pz = 1111102.1024823703
  * px = 2622896.2899722937
  * py = 0.0
  * t_off = 0.0
  * b = 1e-09
 - fit params:
  * pz = 89434.3574034056 ± 5593.011459024442
  * px = 902733.8780739416 ± 8926.72910314118
  * py = 1201192.5543822413 ± 8356.486055156814
  * t_off = 1.6164752771802935e-07 ± 6.142706653946024e-09
  * b = 0.06513162108015248 ± 0.0028877641349278416
 - correlations:
  * (py, t_off) = -0.7214953852963227
  * (py, b) = -0.5047830860377062
  * (pz, px) = -0.4474046152297225
  * (pz, b) = -0.36037931139122353
  * (px, py) = -0.35573004599391317
  * (px, t_off) = -0.16144852862897785
  * (pz, py) = -0.1181179615710654
  * (pz, t_off) = 0.07909335530462434
  * (t_off, b) = 0.34248227223158023
  * (px, b) = 0.5972904737603593
- quality: bad
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False
AnalysisResult
- name: omega_zx
- value: (-1.134+/-0.011)e+05
- quality: bad
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False
AnalysisResult
- name: omega_zy
- value: (-2.070+/-0.009)e+05
- quality: bad
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False
AnalysisResult
- name: omega_zz
- value: (6.9+/-0.6)e+03
- quality: bad
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False
AnalysisResult
- name: omega_ix
- value: (3.02+/-0.11)e+04
- quality: bad
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False
AnalysisResult
- name: omega_iy
- value: (-1.58+/-0.09)e+04
- quality: bad
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False
AnalysisResult
- name: omega_iz
- value: (2.11+/-0.06)e+04
- quality: bad
- extra: <1 items>
- device_components: ['Q17', 'Q30']
- verified: False








### StarkP1Spectroscopy
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm_brisbane
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_brisbane")

# ..jupyter-execute::

# mwc
from qiskit_experiments.library.characterization.t1 import StarkP1Spectroscopy

qubit=13
exp = StarkP1Spectroscopy((qubit,), backend=backend)
exp.set_run_options(shots=100)

step1=False # run carefully if your device is a real device
if step1==True:
    exp_data = exp.run().block_for_results()
    result=exp_data.analysis_results()

    for _ in result:
        print(_)

    exp_data.figure(0)
    
else:
    pass

# retrieve your jobs
from qiskit_experiments.framework import ExperimentData

job_ids= ["cnkf59nq9ax00087ecv0"] # list of job IDs for the experiment
exp_data = ExperimentData(experiment = exp)
exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp.analysis.run(exp_data)
exp_data.block_for_results()

result=exp_data.analysis_results()
exp_data.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/752fc54e-5244-4ac9-927b-a3ba9dc72c09)







### StarkRamseyXY
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm_kyoyo
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_kyoto")

qubit = 6
for qpair in backend.coupling_map:
    if qpair[0] == qubit:
        break       

# ..jupyter-execute::
        
# mwc        
qubit = 6
from qiskit import pulse
from qiskit_experiments.library import StarkRamseyXY

exp = StarkRamseyXY(
    physical_qubits=(qubit,),
    backend=backend,
    )

exp.set_experiment_options(stark_amp=0.5, 
                           stark_channel=pulse.ControlChannel(qubit), 
                          )

exp.set_run_options(shots=1000)

for i in range(2):
    print(exp.circuits()[i])

step1=False   # run carefully if your backend is a real device
if step1==True:
        
    exp_data=exp.run().block_for_results()
    result=exp_data.analysis_results()
    
    for _ in result:
        print(_)
    
    exp_data.figure(0)
    
else:
    pass

# retrieve your jobs
from qiskit_experiments.framework import ExperimentData 

job_ids= ["cnmb50mtakkg008rgkdg"] # list of job IDs for the experiment
exp_data = ExperimentData(experiment=exp)
exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp.analysis.run(exp_data)
exp_data.block_for_results()

exp_data.figure(0)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/7f3ac26b-5ef5-4900-b767-086511bf1852)






### StarkRamseyXYAmpScan
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm_kyoyo
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_kyoto")

qubit = 6
for qpair in backend.coupling_map:
    if qpair[0] == qubit:
        break       

# ..jupyter-execute::
        
# mwc        
qubit = 6
from qiskit import pulse
from qiskit_experiments.library import StarkRamseyXYAmpScan

exp = StarkRamseyXYAmpScan(
    physical_qubits=(qubit,),
    backend=backend,
    )

exp.set_experiment_options(
    stark_channel=pulse.ControlChannel(qubit), 
    )
exp.set_run_options(shots=100)
for i in range(2):
    print(exp.circuits()[i])

step1=False   # run carefully if your backend is a real device
if step1==True:
        
    exp_data=exp.run().block_for_results()
    result=exp_data.analysis_results()
    
    for _ in result:
        print(_)
    
    exp_data.figure(0)
    
else:
    pass

# retrieve your jobs
from qiskit_experiments.framework import ExperimentData # retrieve jove

job_ids= ["cnq8b5m24wx0008fe6vg"] # List of job IDs for the experiment
exp_data = ExperimentData(experiment=exp)
exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
exp.analysis.run(exp_data)
exp_data.block_for_results()
result=exp_data.analysis_results()

exp_data.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/3c988b93-86c6-4205-bdf7-7269858fb247)













