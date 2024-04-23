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
