# Tasks

This repository contains all Tasks and solutions for the kubernetes training.

Every Folder contains 

* a `Readme.md` file with the tasks and some explanations/links/tips.
* a `task.yaml` file containing an initial state. This file mus be applied to the cluster.
    * to apply the file define a namespace `kubectl --namespace yourName -f task.yaml`
* sometimes: a `Solution` folder with all required yamls to fulfill the scenario

## namespace
Select your working namespace like it is described in the [cheatsheet](./cheatsheet.md).

## git bash on windows
If you are using git bash on windows, you need to use `winpty` for executing `kubectl exec -it`:
`winpty ~/bin/kubectl exec -it demo-backend-6dbf9c4f-z6srq -- sh`

## Full-Demo

There is also a Full-Demo folder designed to have the fully finished application deployable via apply.
