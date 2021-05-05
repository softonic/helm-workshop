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
We are going to see in depth the topic about charts and subcharts.

### Gueting started

```sh
git clone git@github.com:softonic/helm-workshop.git
git checkout helm-dependencies
```

### Add first subchart

In `Chart.yaml` add:

```yaml
dependencies:
  - name: redis
    version: 14.1.0
    repository: https://charts.bitnami.com/bitnami
```

And then install the dependencies

```sh
helm dep up
```

### Add second subchart with alias:

In `Chart.yaml` add:

```yaml
...
  - name: redis
    version: 14.1.0
    repository: https://charts.bitnami.com/bitnami
    alias: redis-alias
```

And then install the dependencies

```sh
helm dep up
```

You will see that you have only one file in `charts` folder

And now we will try to up the service:

```sh
helm install --create-namespace -n test-dependencies test-dependencies .
```

You will see that all pods are running as expected.

### Add conditions

Here explain a bit about conditions and tags:

```yaml
# Chart.yaml
dependencies:
      - name: subchart1
        repository: http://localhost:10191
        version: 0.1.0
        condition: subchart1.enabled, global.subchart1.enabled
        tags:
          - front-end        #(chart should be disabled because the tags.front-end is “false” in values.yaml file , but ...)
          - subchart1        #(subchart1.enabled condition path is present in values.yaml file and it has "true" value...)
                             #(this condition, so it overrides tag front-end and this chart will be enabled)

      - name: subchart2
        repository: http://localhost:10191
        version: 0.1.0
        condition: subchart2.enabled,global.subchart2.enabled
                  #(as soon as no one from these paths is exists in values.yaml this condition has ho effect)

        tags:
          - back-end        #(chart should be enabled because the tags.back-end is “true” in values.yaml file)
          - subchart2       #(and there is no condition path found in values.yaml to override it)

# values.yaml
subchart1:
  enabled: true          #(this could be found from requirements as subchart1.enabled and override tags in this case)
tags:
  front-end: false      #(this disables charts with tag front-end)
  back-end: true        #(this enables charts with tag back-end)
```

The logic and sequence of conditions and tags are described in Tags and Condition Resolution:

Conditions (when set in values) always override tags. The first condition path that exists wins and subsequent ones for that chart are ignored.
* Tags are evaluated as ‘if any of the chart’s tags are true then enable the chart’.
* Tags and conditions values must be set in the top parent’s values.
* The tags: key in values must be a top level key. Globals and nested tags: tables are not currently supported.

Now the excercise, lets add some conditions:

```yaml
# Chart.yaml
dependencies:
  - name: redis
    version: 14.1.0
    repository: https://charts.bitnami.com/bitnami
    condition: redis.enabled, not redis-alias.enabled
  - name: redis
    version: 14.1.0
    repository: https://charts.bitnami.com/bitnami
    alias: redis-alias
    condition: redis-alias.enabled

# values.yaml
redis-alias: 
  enabled: true
redis:
  enabled: false
```

### Development flow

Include subchart locally:

```yaml
dependencies:
  - name: redis
    version: 14.1.0
    repository: "file://temp/redis"
```

And extract subchart to the temp folder.
