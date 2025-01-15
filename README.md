# Hello-World

This is ***Naohito Nakazawa's*** first repository on github. I am

## a physicist (Ph.D)

and also a part-time lecturer in Waseda University

a beginner of github* and a beginner of python.

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
