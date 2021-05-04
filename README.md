# helm-workshop
Helm workshop

## CHAPTER 2. Helm Charts
For these exercises, please open the `00-exercises` folder and refer to the `README.md` of each exercise.

Each exercise consists in two folders, one for the initial state and one with the solution. Try and perform the needed changes without consulting the solution.

In order to test your changes, you can check the contents of the generated manifests by using:


```
helm template 00-exercises/02-specificity
```

The last exercise (*06-final-boss*) begins with the chart generated in the previous chapter of the workshop (present in the `helm-workshop` folder at the root of the project).

Note that this last exercise can actually be applied to the kubernetes cluster of your choice (e.g. kind), by running the install / upgrade command:

```
helm upgrade --install helm-workshop helm-workshop

# To apply a custom values file
helm upgrade --install helm-workshop helm-workshop -f helm-workshop/values.overwrite.yaml

# To force using only the original file
helm upgrade --install helm-workshop helm-workshop -f helm-workshop/values.yaml
```
