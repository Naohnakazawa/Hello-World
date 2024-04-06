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

print(backend_data.__dict__)

print()

print(backend_data._backend.__dict__)

print()

print(backend_data._backend.__dict__.get("_defs_dict").get("meas_freq_est"))

print()

print(backend_data._pulse_conf.to_dict())

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

{'_backend': <qiskit_experiments.test.mock_iq_backend.MockIQBackend object at 0x7f901d131f00>, '_v1': False, '_v2': True, '_pulse_conf': <qiskit.providers.models.backendconfiguration.PulseBackendConfiguration object at 0x7f90165e8f40>}

{'_experiment_helper': None, '_rng': Generator(PCG64) at 0x7F901D65F680, '_conf_dict_real': {'backend_name': 'fake_openpulse_2q', 'backend_version': '0.0.0', 'n_qubits': 2, 'basis_gates': ['u1', 'u2', 'u3', 'cx', 'id'], 'gates': [{'name': 'TODO', 'parameters': [], 'qasm_def': 'TODO'}], 'local': True, 'simulator': False, 'conditional': True, 'open_pulse': True, 'memory': False, 'max_shots': 65536, 'coupling_map': [[0, 1]], 'dynamic_reprate_enabled': False, 'n_registers': 1, 'description': 'A fake test backend with pulse defaults', 'dt': 1.3333, 'dtm': 10.500000000000002, 'processor_type': {'family': 'Canary', 'revision': '1.0', 'segment': 'A'}, 'n_uchannels': 2, 'u_channel_lo': [[{'q': 0, 'scale': (1+0j)}], [{'q': 0, 'scale': (-1+0j)}, {'q': 1, 'scale': (1+0j)}]], 'meas_levels': [0, 1, 2], 'qubit_lo_range': [[4.5, 5.5], [4.5, 5.5]], 'meas_lo_range': [[6.0, 7.0], [6.0, 7.0]], 'meas_kernels': ['kernel1'], 'discriminators': ['max_1Q_fidelity'], 'rep_times': [99.99999999999999, 250.0, 500.0, 1000.0], 'channel_bandwidth': [[-0.2, 0.4], [-0.30000000000000004, 0.30000000000000004], [-0.30000000000000004, 0.30000000000000004], [-0.02, 0.02], [-0.02, 0.02], [-0.02, 0.02]], 'meas_map': [[0, 1]], 'acquisition_latency': [[100, 100], [100, 100]], 'conditional_latency': [[100, 1000], [1000, 100], [100, 1000], [1000, 100], [100, 1000], [1000, 100]], 'hamiltonian': {'h_str': ['np.pi*(2*v0-alpha0)*O0', 'np.pi*alpha0*O0*O0', '2*np.pi*r*X0||D0', '2*np.pi*r*X0||U1', '2*np.pi*r*X1||U0', 'np.pi*(2*v1-alpha1)*O1', 'np.pi*alpha1*O1*O1', '2*np.pi*r*X1||D1', '2*np.pi*j*(Sp0*Sm1+Sm0*Sp1)'], 'description': 'A hamiltonian for a mocked 2Q device, with 1Q and 2Q terms.', 'qub': {'0': 3, '1': 3}, 'vars': {'v0': 5.0, 'v1': 5.1000000000000005, 'j': 0.01, 'r': 0.02, 'alpha0': -0.33, 'alpha1': -0.33}}, 'channels': {'acquire0': {'operates': {'qubits': [0]}, 'purpose': 'acquire', 'type': 'acquire'}, 'acquire1': {'operates': {'qubits': [1]}, 'purpose': 'acquire', 'type': 'acquire'}, 'd0': {'operates': {'qubits': [0]}, 'purpose': 'drive', 'type': 'drive'}, 'd1': {'operates': {'qubits': [1]}, 'purpose': 'drive', 'type': 'drive'}, 'm0': {'type': 'measure', 'purpose': 'measure', 'operates': {'qubits': [0]}}, 'm1': {'type': 'measure', 'purpose': 'measure', 'operates': {'qubits': [1]}}, 'u0': {'operates': {'qubits': [0, 1]}, 'purpose': 'cross-resonance', 'type': 'control'}, 'u1': {'operates': {'qubits': [1, 0]}, 'purpose': 'cross-resonance', 'type': 'control'}}}, '_props_dict': {'backend_name': 'fake_openpulse_2q', 'backend_version': '0.0.0', 'last_update_date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'qubits': [[{'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'T1', 'unit': 'µs', 'value': 71.9500421005539}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'T2', 'unit': 'µs', 'value': 69.4240447362455}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'frequency', 'unit': 'MHz', 'value': 4919.96800692}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'readout_error', 'unit': '', 'value': 0.02}], [{'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'T1', 'unit': 'µs', 'value': 81.9500421005539}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'T2', 'unit': 'µs', 'value': 75.5598482446578}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'frequency', 'unit': 'GHz', 'value': 5.01996800692}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'readout_error', 'unit': '', 'value': 0.02}]], 'gates': [{'qubits': [0], 'gate': 'id', 'parameters': [{'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_error', 'unit': '', 'value': 0}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_length', 'unit': 'ns', 'value': 2.6666}]}, {'qubits': [1], 'gate': 'id', 'parameters': [{'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_error', 'unit': '', 'value': 0}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_length', 'unit': 'ns', 'value': 2.6666}]}, {'qubits': [0], 'gate': 'u1', 'parameters': [{'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_error', 'unit': '', 'value': 0.06}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_length', 'unit': 'ns', 'value': 0.0}]}, {'qubits': [1], 'gate': 'u1', 'parameters': [{'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_error', 'unit': '', 'value': 0.06}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_length', 'unit': 'ns', 'value': 0.0}]}, {'qubits': [0], 'gate': 'u2', 'parameters': [{'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_error', 'unit': '', 'value': 0.06}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_length', 'unit': 'ns', 'value': 2.6666}]}, {'qubits': [1], 'gate': 'u2', 'parameters': [{'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_error', 'unit': '', 'value': 0.06}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_length', 'unit': 'ns', 'value': 2.6666}]}, {'qubits': [0], 'gate': 'u3', 'parameters': [{'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_error', 'unit': '', 'value': 0.06}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_length', 'unit': 'ns', 'value': 5.3332}]}, {'qubits': [1], 'gate': 'u3', 'parameters': [{'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_error', 'unit': '', 'value': 0.06}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_length', 'unit': 'ns', 'value': 5.3332}]}, {'qubits': [0, 1], 'gate': 'cx', 'parameters': [{'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_error', 'unit': '', 'value': 1.0}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_length', 'unit': 'ns', 'value': 29.3326}]}, {'qubits': [1], 'gate': 'u1', 'parameters': [{'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_error', 'unit': '', 'value': 0.06}, {'date': datetime.datetime(2024, 4, 6, 10, 58, 23, 342002), 'name': 'gate_length', 'unit': 'ns', 'value': 0.0}]}], 'general': []}, '_defs_dict': {'qubit_freq_est': [4.9, 5.0], 'meas_freq_est': [6.5, 6.6000000000000005], 'buffer': 10, 'pulse_library': [{'name': 'x90p_d0', 'samples': [(0.1+0j), (0.1+0j)]}, {'name': 'x90p_d1', 'samples': [(0.1+0j), (0.1+0j)]}, {'name': 'x90m_d0', 'samples': [(-0.1+0j), (-0.1+0j)]}, {'name': 'x90m_d1', 'samples': [(-0.1+0j), (-0.1+0j)]}, {'name': 'y90p_d0', 'samples': [0.1j, 0.1j]}, {'name': 'y90p_d1', 'samples': [0.1j, 0.1j]}, {'name': 'xp_d0', 'samples': [(0.2+0j), (0.2+0j)]}, {'name': 'ym_d0', 'samples': [(-0-0.2j), (-0-0.2j)]}, {'name': 'cr90p_u0', 'samples': [(0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j)]}, {'name': 'cr90m_u0', 'samples': [(-0.1+0j), (-0.1+0j), (-0.1+0j), (-0.1+0j), (-0.1+0j), (-0.1+0j), (-0.1+0j), (-0.1+0j), (-0.1+0j)]}, {'name': 'measure_m0', 'samples': [(0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j)]}, {'name': 'measure_m1', 'samples': [(0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j), (0.1+0j)]}], 'cmd_def': [{'name': 'u1', 'qubits': [0], 'sequence': [{'name': 'fc', 't0': 0, 'ch': 'd0', 'phase': '-P0'}]}, {'name': 'u1', 'qubits': [1], 'sequence': [{'name': 'fc', 't0': 0, 'ch': 'd1', 'phase': '-P0'}]}, {'name': 'u2', 'qubits': [0], 'sequence': [{'name': 'fc', 't0': 0, 'ch': 'd0', 'phase': '-P1'}, {'name': 'y90p_d0', 't0': 0, 'ch': 'd0'}, {'name': 'fc', 't0': 2, 'ch': 'd0', 'phase': '-P0'}]}, {'name': 'u2', 'qubits': [1], 'sequence': [{'name': 'fc', 't0': 0, 'ch': 'd1', 'phase': '-P1'}, {'name': 'y90p_d1', 't0': 0, 'ch': 'd1'}, {'name': 'fc', 't0': 2, 'ch': 'd1', 'phase': '-P0'}]}, {'name': 'u3', 'qubits': [0], 'sequence': [{'name': 'fc', 't0': 0, 'ch': 'd0', 'phase': '-P2'}, {'name': 'x90p_d0', 't0': 0, 'ch': 'd0'}, {'name': 'fc', 't0': 2, 'ch': 'd0', 'phase': '-P0'}, {'name': 'x90m_d0', 't0': 2, 'ch': 'd0'}, {'name': 'fc', 't0': 4, 'ch': 'd0', 'phase': '-P1'}]}, {'name': 'u3', 'qubits': [1], 'sequence': [{'name': 'fc', 't0': 0, 'ch': 'd1', 'phase': '-P2'}, {'name': 'x90p_d1', 't0': 0, 'ch': 'd1'}, {'name': 'fc', 't0': 2, 'ch': 'd1', 'phase': '-P0'}, {'name': 'x90m_d1', 't0': 2, 'ch': 'd1'}, {'name': 'fc', 't0': 4, 'ch': 'd1', 'phase': '-P1'}]}, {'name': 'cx', 'qubits': [0, 1], 'sequence': [{'name': 'fc', 't0': 0, 'ch': 'd0', 'phase': 1.57}, {'name': 'ym_d0', 't0': 0, 'ch': 'd0'}, {'name': 'xp_d0', 't0': 11, 'ch': 'd0'}, {'name': 'x90p_d1', 't0': 0, 'ch': 'd1'}, {'name': 'cr90p_u0', 't0': 2, 'ch': 'u0'}, {'name': 'cr90m_u0', 't0': 13, 'ch': 'u0'}]}, {'name': 'measure', 'qubits': [0, 1], 'sequence': [{'name': 'measure_m0', 't0': 0, 'ch': 'm0'}, {'name': 'measure_m1', 't0': 0, 'ch': 'm1'}, {'name': 'acquire', 't0': 0, 'duration': 10, 'qubits': [0, 1], 'memory_slot': [0, 1]}]}], '_data': {}}, '_options': Options(shots=1024, meas_level=<MeasLevel.KERNELED: 1>, meas_return='single'), '_provider': None, 'name': 'fake_openpulse_2q', 'description': 'A fake test backend with pulse defaults', 'online_date': None, 'backend_version': '0.0.0', '_coupling_map': None, '_target': None, 'sim': None, 'channels_map': {'acquire': defaultdict(<class 'list'>, {(0,): [AcquireChannel(0)], (1,): [AcquireChannel(1)]}), 'drive': defaultdict(<class 'list'>, {(0,): [DriveChannel(0)], (1,): [DriveChannel(1)]}), 'measure': defaultdict(<class 'list'>, {(0,): [MeasureChannel(0)], (1,): [MeasureChannel(1)]}), 'control': defaultdict(<class 'list'>, {(0, 1): [ControlChannel(0)], (1, 0): [ControlChannel(1)]})}, '_defaults': <qiskit.providers.models.pulsedefaults.PulseDefaults object at 0x7f901d6aa650>}

[6.5, 6.6000000000000005]

{'backend_name': 'fake_openpulse_2q', 'backend_version': '0.0.0', 'n_qubits': 2, 'basis_gates': ['u1', 'u2', 'u3', 'cx', 'id'], 'gates': [{'name': 'TODO', 'parameters': [], 'qasm_def': 'TODO'}], 'local': True, 'simulator': False, 'conditional': True, 'open_pulse': True, 'memory': False, 'max_shots': 65536, 'coupling_map': [[0, 1]], 'dynamic_reprate_enabled': False, 'n_registers': 1, 'description': 'A fake test backend with pulse defaults', 'dt': 1.3333, 'dtm': 10.500000000000004, 'processor_type': {'family': 'Canary', 'revision': '1.0', 'segment': 'A'}, 'n_uchannels': 2, 'u_channel_lo': [[{'q': 0, 'scale': (1+0j)}], [{'q': 0, 'scale': (-1+0j)}, {'q': 1, 'scale': (1+0j)}]], 'meas_levels': [0, 1, 2], 'qubit_lo_range': [[4.5, 5.5], [4.5, 5.5]], 'meas_lo_range': [[6.0, 7.0], [6.0, 7.0]], 'meas_kernels': ['kernel1'], 'discriminators': ['max_1Q_fidelity'], 'rep_times': [99.99999999999997, 250.0, 500.0, 1000.0], 'channel_bandwidth': [[-0.2, 0.4], [-0.3000000000000001, 0.3000000000000001], [-0.3000000000000001, 0.3000000000000001], [-0.02, 0.02], [-0.02, 0.02], [-0.02, 0.02]], 'meas_map': [[0, 1]], 'acquisition_latency': [[100, 100], [100, 100]], 'conditional_latency': [[100, 1000], [1000, 100], [100, 1000], [1000, 100], [100, 1000], [1000, 100]], 'hamiltonian': {'h_str': ['np.pi*(2*v0-alpha0)*O0', 'np.pi*alpha0*O0*O0', '2*np.pi*r*X0||D0', '2*np.pi*r*X0||U1', '2*np.pi*r*X1||U0', 'np.pi*(2*v1-alpha1)*O1', 'np.pi*alpha1*O1*O1', '2*np.pi*r*X1||D1', '2*np.pi*j*(Sp0*Sm1+Sm0*Sp1)'], 'description': 'A hamiltonian for a mocked 2Q device, with 1Q and 2Q terms.', 'qub': {'0': 3, '1': 3}, 'vars': {'v0': 5.0, 'v1': 5.100000000000001, 'j': 0.01, 'r': 0.02, 'alpha0': -0.33, 'alpha1': -0.33}}, 'channels': {'acquire0': {'operates': {'qubits': [0]}, 'purpose': 'acquire', 'type': 'acquire'}, 'acquire1': {'operates': {'qubits': [1]}, 'purpose': 'acquire', 'type': 'acquire'}, 'd0': {'operates': {'qubits': [0]}, 'purpose': 'drive', 'type': 'drive'}, 'd1': {'operates': {'qubits': [1]}, 'purpose': 'drive', 'type': 'drive'}, 'm0': {'type': 'measure', 'purpose': 'measure', 'operates': {'qubits': [0]}}, 'm1': {'type': 'measure', 'purpose': 'measure', 'operates': {'qubits': [1]}}, 'u0': {'operates': {'qubits': [0, 1]}, 'purpose': 'cross-resonance', 'type': 'control'}, 'u1': {'operates': {'qubits': [1, 0]}, 'purpose': 'cross-resonance', 'type': 'control'}}}

1

AcquireChannel(1)

[ControlChannel(0)]

DriveChannel(1)

[4919968006.92, 5019968006.92]

1.3333e-09

1

[]

MeasureChannel(1)

1

fake_openpulse_2q

2

1

8.19500421005539e-05

2
