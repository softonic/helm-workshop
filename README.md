# helm-workshop
Helm workshop

## CHAPTER 1. What is helm
In this section we will learn the `helm` basics, and we will create our first chart.
Exercises are described in the slides.
After completing the exercises, you should have a chart such as the one in the `helm-workshop` folder.

## CHAPTER 2. Helm Charts
For these exercises, please open the `02-exercises` folder and refer to the `README.md` of each exercise.

Each exercise consists in two folders, one for the initial state and one with the solution. Try and perform the needed changes without consulting the solution.

In order to test your changes, you can check the contents of the generated manifests by using:


```
helm template 02-exercises/02-specificity
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

Please note that you don't need to finish all the exercises in order to continue to the next chapter. You can simply use the `helm-workshop` chart as a reference.

## CHAPTER 3. Helm Secrets
Here we'll understand how `helm secrets` works. Which are the tools used underneath, and how to use them with a local pgp key.

Please open the `03-exercises` folder. You'll find the same exercise we were working in the Helm Charts exercise and some instructions in the `README.md` file.

Note: The execution of this exercise is not a requirement for exercise in Chapter 4.

## CHAPTER 4. Packages
TODO
