[//]: # (title: Add dependencies on a Pod library)

<tldr>

   * Before adding Pod dependencies, [complete the initial configuration](native-cocoapods.md#set-up-an-environment-to-work-with-cocoapods).
   * You can find a sample project in our [GitHub repository](https://github.com/Kotlin/kmp-with-cocoapods-sample).

</tldr>

You can add dependencies on Pod libraries from different locations in your Kotlin project.

To add a Pod dependency, call the `pod()` function in the shared module's `build.gradle(.kts)` file.
Each dependency requires a separate function call. You can specify the parameters for the dependency in
the configuration block of the function.

* When you add a new dependency and re-import the project in your IDE, the library will be connected automatically.
* To use your Kotlin project with Xcode, [make changes in your project's Podfile](native-cocoapods.md#update-podfile-for-xcode) first.

> If you don't specify the minimum deployment target version and a dependency Pod requires a higher deployment target,
> you'll get an error.
>
{style="note"}

## From the CocoaPods repository

To add a dependency on a Pod library located in the CocoaPods repository: 

1. Specify the name of a Pod library in the `pod()` function.
   
   In the configuration block, you can specify the version of the library using the `version` parameter.
   To use the latest version of the library, you can omit this parameter altogether.

   > You can add dependencies on subspecs as well.
   >
   {style="note"}

2. Specify the minimum deployment target version for the Pod library.

    ```kotlin
    kotlin {
        iosArm64()

        cocoapods {
            version = "2.0"
            summary = "CocoaPods test library"
            homepage = "https://github.com/JetBrains/kotlin"
            ios.deploymentTarget = "16.0"

            pod("SDWebImage") {
                version = "5.20.0"
            }
        }
    }
    ```

3. Run **Build** | **Reload All Gradle Projects** in IntelliJ IDEA (or **File** | **Sync Project with Gradle Files** in Android Studio)
   to re-import the project.

To use these dependencies from the Kotlin code, import the packages `cocoapods.<library-name>`:

```kotlin
import cocoapods.SDWebImage.*
```

## On a locally stored library

To add a dependency on a locally stored Pod library:

1. Specify the name of a Pod library in the `pod()` function.

   In the configuration block, specify the path to the local Pod library: use the `path()` function in the `source` parameter value.

   > You can add local dependencies on subspecs as well.
   > The `cocoapods {}` block can include dependencies to Pods stored locally and Pods from the CocoaPods repository at
   > the same time.
   >
   {style="note"}

2. Specify the minimum deployment target version for the Pod library.

    ```kotlin
    kotlin {
        iosArm64()

        cocoapods {
            version = "2.0"
            summary = "CocoaPods test library"
            homepage = "https://github.com/JetBrains/kotlin"
            ios.deploymentTarget = "16.0"

            pod("pod_dependency") {
                version = "1.0"
                extraOpts += listOf("-compiler-option")
                source = path(project.file("../pod_dependency"))
            }
            pod("subspec_dependency/Core") {
                version = "1.0"
                extraOpts += listOf("-compiler-option")
                source = path(project.file("../subspec_dependency"))
            }
            pod("SDWebImage") {
                version = "5.20.0"
            }
        }
    }
    ```

   > You can also specify the version of the library using `version` parameter in the configuration block.
   > To use the latest version of the library, omit the parameter.
   >
   {style="note"}

3. Run **Build** | **Reload All Gradle Projects** in IntelliJ IDEA (or **File** | **Sync Project with Gradle Files** in Android Studio)
   to re-import the project.

To use these dependencies from the Kotlin code, import the packages `cocoapods.<library-name>`:

```kotlin
import cocoapods.pod_dependency.*
import cocoapods.subspec_dependency.*
import cocoapods.SDWebImage.*
```

## From a custom Git repository

To add a dependency on a Pod library located in the custom Git repository:

1. Specify the name of a Pod library in the `pod()` function.

   In the configuration block, specify the path to the git repository: use the `git()` function in the `source` parameter value.

   Additionally, you can specify the following parameters in the block after `git()`:
    * `commit` – to use a specific commit from the repository
    * `tag` – to use a specific tag from the repository
    * `branch` – to use a specific branch from the repository

   The `git()` function prioritizes passed parameters in the following order: `commit`, `tag`, `branch`.
   If you don't specify a parameter, the Kotlin plugin uses `HEAD` from the `master` branch.

   > You can combine `branch`, `commit`, and `tag` parameters to get the specific version of a Pod.
   >
   {style="note"}

2. Specify the minimum deployment target version for the Pod library.

    ```kotlin
    kotlin {
        iosArm64()

        cocoapods {
            version = "2.0"
            summary = "CocoaPods test library"
            homepage = "https://github.com/JetBrains/kotlin"
            ios.deploymentTarget = "16.0"

            pod("SDWebImage") {
                source = git("https://github.com/SDWebImage/SDWebImage") {
                    tag = "5.20.0"
                }
            }

            pod("JSONModel") {
                source = git("https://github.com/jsonmodel/jsonmodel.git") {
                    branch = "key-mapper-class"
                }
            }

            pod("CocoaLumberjack") {
                source = git("https://github.com/CocoaLumberjack/CocoaLumberjack.git") {
                    commit = "3e7f595e3a459c39b917aacf9856cd2a48c4dbf3"
                }
            }
        }
    }
    ```

3. Run **Build** | **Reload All Gradle Projects** in IntelliJ IDEA (or **File** | **Sync Project with Gradle Files** in Android Studio)
   to re-import the project.

To use these dependencies from the Kotlin code, import the packages `cocoapods.<library-name>`:

```kotlin
import cocoapods.SDWebImage.*
import cocoapods.JSONModel.*
import cocoapods.CocoaLumberjack.*
```

## From a custom Podspec repository

To add a dependency on a Pod library located in the custom Podspec repository:

1. Specify the address of the custom Podspec repository using a `url()` call inside the `specRepos {}` block.
2. Specify the name of a Pod library in the `pod()` function.
3. Specify the minimum deployment target version for the Pod library.

    ```kotlin
    kotlin {
        iosArm64()

        cocoapods {
            version = "2.0"
            summary = "CocoaPods test library"
            homepage = "https://github.com/JetBrains/kotlin"
            ios.deploymentTarget = "16.0"

            specRepos {
                url("https://github.com/Kotlin/kotlin-cocoapods-spec.git")
            }
            pod("example")
        }
    }
    ```

4. Run **Build** | **Reload All Gradle Projects** in IntelliJ IDEA (or **File** | **Sync Project with Gradle Files** in Android Studio)
   to re-import the project.

> To work with Xcode, specify the location of specs at the beginning of your Podfile:
> 
> ```ruby
> source 'https://github.com/Kotlin/kotlin-cocoapods-spec.git'
> ```
>
{style="note"}

To use these dependencies from the Kotlin code, import the packages `cocoapods.<library-name>`:

```kotlin
import cocoapods.example.*
```

## With custom cinterop options

To add a dependency on a Pod library using custom cinterop options:

1. Specify the name of a Pod library in the `pod()` function.
2. In the configuration block, add the following options:

   * `extraOpts` – to specify the list of options for a Pod library. For example, `extraOpts = listOf("-compiler-option")`.
      
      > If you encounter issues with clang modules, add the `-fmodules` option as well.
      >
     {style="note"}

   * `packageName` – to import the library directly using the package name with `import <packageName>`.

3. Specify the minimum deployment target version for the Pod library.

    ```kotlin
    kotlin {
        iosArm64()

        cocoapods {
            version = "2.0"
            summary = "CocoaPods test library"
            homepage = "https://github.com/JetBrains/kotlin"
            ios.deploymentTarget = "16.0"

            pod("FirebaseAuth") {
                packageName = "FirebaseAuthWrapper"
                version = "11.7.0"
                extraOpts += listOf("-compiler-option", "-fmodules")
            }
        }
    }
    ```

4. Run **Build** | **Reload All Gradle Projects** in IntelliJ IDEA (or **File** | **Sync Project with Gradle Files** in Android Studio)
   to re-import the project.

To use these dependencies from the Kotlin code, import the packages `cocoapods.<library-name>`:
   
```kotlin
import cocoapods.FirebaseAuth.*
```
   
If you use the `packageName` parameter, you can import the library using the package name `import <packageName>`:
   
```kotlin
import FirebaseAuthWrapper.Auth
import FirebaseAuthWrapper.User
```

### Support for Objective-C headers with @import directives

> This feature is [Experimental](components-stability.md#stability-levels-explained).
> It may be dropped or changed at any time. Use it only for evaluation purposes.
> We'd appreciate your feedback on it in [YouTrack](https://kotl.in/issue).
>
{style="warning"}

Some Objective-C libraries, specifically those that serve as wrappers for Swift libraries,
have `@import` directives in their headers. By default, cinterop doesn't provide support for these directives.

To enable support for `@import` directives, specify the `-fmodules` option in the configuration block of the `pod()` function:

```kotlin
kotlin {
    iosArm64()

    cocoapods {
        version = "2.0"
        summary = "CocoaPods test library"
        homepage = "https://github.com/JetBrains/kotlin"
        ios.deploymentTarget = "16.0"

        pod("PodName") {
            version = "1.0.0"
            extraOpts = listOf("-compiler-option", "-fmodules")
        }
    }
}
```

### Share Kotlin cinterop between dependent Pods

If you add multiple dependencies on Pods using the `pod()` function, you might encounter issues when
there are dependencies between APIs of your Pods.

To make the code compile in such cases, use the `useInteropBindingFrom()` function.
It utilizes the cinterop binding generated for another Pod while building a binding for the new Pod.

You should declare the dependent Pod before setting up the dependency:

```kotlin
// The cinterop of pod("WebImage"):
fun loadImage(): WebImage

// The cinterop of pod("Info"):
fun printImageInfo(image: WebImage)

// Your code:
printImageInfo(loadImage())
```

If you haven't configured the correct dependencies between cinterops in this case,
the code would be invalid because the `WebImage` type would be sourced from different cinterop files and, consequently,
different packages.

## What's next

* [Set up dependencies between a Kotlin project and an Xcode project](native-cocoapods-xcode.md)
* [See the full CocoaPods Gradle plugin DSL reference](native-cocoapods-dsl-reference.md)