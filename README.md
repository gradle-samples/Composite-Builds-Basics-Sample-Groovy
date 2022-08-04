## Composite Builds Basics Sample Groovy

### Defining and using a composite build

This sample shows how 2 Gradle builds that are normally developed separately and combined using binary integration can be wired together into a composite build with source integration. The `my-utils` multiproject build produces 2 different java libraries, and the `my-app` build produces an executable using functions from those libraries.

Note that the `my-app` build does not have direct dependencies on `my-utils`. Instead, it declares module dependencies on the libraries produced by `my-utils`:

my-app/app/build.gradle:
```groovy
plugins {
    id 'application'
}

application {
    mainClass = 'org.sample.myapp.Main'
}

dependencies {
    implementation 'org.sample:number-utils:1.0'
    implementation 'org.sample:string-utils:1.0'
}
```

### Using command-line composite build

When using a composite build, no shared repository is required for the builds, and no changes need to be made to the build scripts.

1. Change the sources of `Number.java`
2. Run the `my-app` application, including the `my-utils` build.

```bash
cd my-app
gradle --include-build ../my-utils run
```

Using _dependency substitution_, the module dependencies on the util libraries are replaced by project dependencies on `my-utils`.

## Converting `my-app` to a composite build

It's possible to make the above arrangement persistent, by making `my-app` a composite build that includes `my-utils`.

```bash
cd my-app
echo "includeBuild '../my-utils'" >> settings.gradle
gradle run
```

With this configuration, the module dependencies from `my-app` to `my-utils` will always be substituted with project dependencies.

While simple, this approach has the downside of modifying the `my-app` build.

## Using separate composite build

It is also possible to create a separate composite build that includes both the `my-app` and `my-utils` builds.

settings.gradle:
```groovy
rootProject.name = 'my-composite'

includeBuild 'my-app'
includeBuild 'my-utils'
```


After doing so, you can reference any tasks in the included builds directly on the command line in order to execute.

```bash
gradle :my-app:app:run
```

Alternately, you can create delegating tasks in the composite project to execute tasks without specifying the full task path.


```bash
gradle run
```
