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
One of the candidates may be FineZXAmplitude(Cal) experiment. 













