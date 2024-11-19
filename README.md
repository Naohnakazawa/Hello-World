# Hello-World

This is ***Naohito Nakazawa's*** first repository on github. I am

## *a physicist* 
### *a part-time lecturer in Waseda University*

*a beginner of github* and also, 
*a beginner of python*

This is a <sub>subscript</sub> text.

This is a <sup>superscript</sup> test.

Some basic **Git** commands are:
```
git config
git branch
git checkout # dangerous! 
git status
git add
git commit
git restore  # dangerous!
git diff
git log 
git remote
git tag
git show
```

An interesting paper is [here](https://arxiv.org/abs/2308.02321).



### Git commit 

This is a test case#1=origin/main/README.md update
>	local/main pull

results ff(fasr-forward)

This is a test case#2=local/hello-world-0515/README.md update
>	git add + git commit

may be necessary

This is a test case#3 merge local/hello-world-0515 to local/main
>	git checkout main
>
>	git merge local/hello-world-0515

results ff 

This is a test case#4 loal/main push
>	git push origin main

results ff (merge local/main to origin/main without commit)

This is a test case#5 origin/branch=my-workflows-0516 work on this branch only without use of local branches
> git commit "work on origin/branch"

results one commit

### Question 1

How do we implement a "cx" gate insturuction to backend.target in DynamicsBackend ?
Following the tutorial (https://qiskit-community.github.io/qiskit-dynamics/tutorials/dynamics_backend.html, we have 

```
print(backend.target)
```

Target
Number of qubits: 2
Instructions:
	measure
		(0,):
			With pulse schedule calibration
		(1,):
			With pulse schedule calibration
	x
		(0,)
		(1,)
	sx
		(0,)
		(1,)
	cx
		(0, 1)
		(1, 0)
	rz
		(0,):
			With pulse schedule calibration
		(1,):
			With pulse schedule calibration



```
backend.target.update_from_instruction_schedule_map(cals.get_inst_map())
print(backend.target)
```



Target
Number of qubits: 2
Instructions:
	measure
		(0,):
			With pulse schedule calibration
		(1,):
			With pulse schedule calibration
	x
		(0,):
			Duration: 3.5555555555555554e-08 sec.
			With pulse schedule calibration
		(1,):
			Duration: 3.5555555555555554e-08 sec.
			With pulse schedule calibration
	sx
		(0,):
			Duration: 3.5555555555555554e-08 sec.
			With pulse schedule calibration
		(1,):
			Duration: 3.5555555555555554e-08 sec.
			With pulse schedule calibration
	cx
		(0, 1)
		(1, 0)
	rz
		(0,):
			With pulse schedule calibration
		(1,):
			With pulse schedule calibration

It seems that we need some calibration experiments for two-qubit gates!
One of the candidates may be FineZXAmplitude(Cal) experiment. As for the pulse definition of the cx-gate, the conversations between Will Shanks, Christopher J Wood and Ali Javadi-Abhari on Qiskit/qiskit/issue#9234(and also #9235) are suggestive. See, also qiskit-experiments/qiskit-experiments/calibration-management/basis-gate-library/EchoedCrossResonance.

Rederences: S. Sheldon et al, PRA93(2016)060302; N.Sundaresan et al, PRX Quantum1,(2020)020318.

### PR reno report (revised)

other:
  - |
    Added minimal working code examples to the API pages for Experiments,
    EFSpectroscopy,
    EFRabi,
    StarkRamseyXYAmpScan,
    FineAmplitudeCal,
    FineXAmplitudeCal,
    FineSXAmplitudeCal,
    FineDragCal,
    FineXDragCal,
    FineSXDragCal,
    FineFrequencyCal,
    FrequencyCal,
    HalfAngleCal,
    RoughAmplitudeCal,
    RoughXSXAmplitudeCal,
    EFRoughXSXAmplitudeCal,
    RoughDragCal,
    RoughFrequencyCal,
    QuantumVolume,
    InterleavedRB,
    LayerFidelity,
    StandardRB,
    MitigatedProcessTomography,
    MitigatedStateTomography,
    ProcessTomography,
    StateTomography and
    TomographyExperiment.
    By default, the backend used in the code examples, are simulators 
    such as, SingleTransmonTestBackend() and
    AerSimulator(FakePerth()).

### test

This is a test of GPG signature from macbookpro.


### PR for Qiskit-Community/qiskit-experiments issue#1238

PR title : Add minimal working code expamples in experiment API docs

### Summary

This change adds minimal working code examples to the API pages for Experiments,
    EFSpectroscopy,
    EFRabi,
    StarkRamseyXYAmpScan,
    FineAmplitudeCal,
    FineXAmplitudeCal,
    FineSXAmplitudeCal,
    FineDragCal,
    FineXDragCal,
    FineSXDragCal,
    FineFrequencyCal,
    FrequencyCal,
    HalfAngleCal,
    RoughAmplitudeCal,
    RoughXSXAmplitudeCal,
    EFRoughXSXAmplitudeCal,
    RoughDragCal,
    RoughFrequencyCal,
    QuantumVolume,
    InterleavedRB,
    LayerFidelity,
    StandardRB,
    MitigatedProcessTomography,
    MitigatedStateTomography,
    ProcessTomography,
    StateTomography and
    TomographyExperiment.

### Details and comments

- This change solves the `issue#1238` (See also the `issue#1221`).
- In the API pages, the documentation for each experiment has been updated with its code example. Users are required to specify a backend in the code example. By default, the backend used in the code examples, are simulators such as, SingleTransmonTestBackend() and AerSimulator(FakePerth()).


### PR checklist (delete when all criteria are met)

- [*] I have read the contributing guide `CONTRIBUTING.md`.
- [ ] I have added the tests to cover my changes.
- [*] I have updated the documentation accordingly.
- [*] I have added a release note file using `reno` if this change needs to be documented in the release notes.



