# Helm dependencies:

## Getting started

```sh
git clone git@github.com:softonic/helm-workshop.git
git checkout helm-dependencies
```

Install kind and up kubernetes in locale:

```sh
ctlptl apply -f "https://ctlptl.softonic.io/kind.yaml"
```

# Add first subchart

Search for redis official helm package. You can search for using:
```
helm search repo <package>
```
or
```
helm search hub --endpoint=... <package>
```

Now, in `Chart.yaml` add:

```yaml
dependencies:
  - name: <chart-name>
    version: <chart-version>
    repository: <repo-url>
```

And then install the dependencies

```sh
helm dep up
```

# Add second subchart with alias:

In `Chart.yaml` add:

```yaml
...
  - name: redis
    version: 14.1.0
    repository: https://charts.bitnami.com/bitnami
    alias: redis-v2
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

# Add conditions

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
    condition: redis.enabled, not redis-v2.enabled
  - name: redis
    version: 14.1.0
    repository: https://charts.bitnami.com/bitnami
    alias: redis-v2
    condition: redis-v2.enabled

# values.yaml
redis-alias: 
  enabled: true
redis:
  enabled: false
```

# Development flow

## Helm git repository

``` 
~/your-repositories-folder $ git clone git@bitbucket.org:softonic-development/helm-laravel.git
```

Include subchart locally:

```yaml
dependencies:
  - name: laravel
    version: 9.5.0
    repository: "file://~/your-repositories-folder/helm-laravel"
```
