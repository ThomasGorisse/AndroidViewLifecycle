# Android View Lifecycle Extensions
Extensions for Android View class that let you access a view lifecycle without having to create a custom view (extend a View)

[![Maven Central](https://img.shields.io/maven-central/v/com.gorisse.thomas/android-view-lifecycle.svg?label=Maven%20Central)](https://search.maven.org/search?q=g:%22com.gorisse.thomas%22%20AND%20a:%22android-view-lifecycle%22)

## Dependency

*app/build.gradle*
```gradle
dependencies {
    implementation "com.gorisse.thomas:android-view-lifecycle:1.0.0"
}
```

## Usage

### View Class Kotlin Extensions

No need for custom view since all properties and functions are defined as View extensions.
```kotlin
val lifecycle = findViewById(R.id.frameLayout).lifecycle
```

```kotlin
val imageView = ImageView(context)
imageView.lifecycleScope.launchWhenCreated {
    loadSlideShow(imageView.getWidth(), imageView.getHeight())
}
```

### View Lifecycle
Listen or execute when a View lifecycle state occured

- Invoke the given action every time the view becomes resumed

```kotlin
view.lifecycle.addObserver(onResume = {
    refreshData()
})
```

- Invoke the given actions once, when the view is at least at a state

If the view is already resumed the action will be performed immediately, otherwise the action will be performed after the view is next resumed
In all the cases, your action will be invoked only one time.

```kotlin
view.doOnResume {
    animate(view)
}
```

Perform the given action when this view is destoyed.

```kotlin
view.doOnDestroy {
    removeListener(view)
}
```

### View LifecycleScope
Launch a coroutine and auto cancel it when the view sate is destroyed

- Launches and runs the given block when the view Lifecycle state is at least resumed.

The returned Job will be cancelled when the View is destroyed.

= *When the view is detached or the Fragment or FragmentActivity container is destroyed, if `loadNetworkData()` is not finished, the network call is cancelled.*

```kotlin
texView.lifecycleScope.launchWhenCreated { // Launch on Dispatchers.Main
    val data = withContext(Dispatchers.IO) { // Dispatchers.IO for background task
        loadNetworkData() // Suspend function making http calls
    }
    texView.text = data // Dispatchers.Main for UI change
}
```

- Launch a repeating call and auto cancel it when the view is destroyed (**TimerTask Coroutine equivalent**)

The returned Job will be cancelled when the View is destroyed.

= *When the view is detached or the Fragment or FragmentActivity container is destroyed, `updateTime(textView)` is not called anymore.*

```kotlin
textView.lifecycleScope.launchWhenCreated {
    while (isActive) {
        view.text = "${System.currentTimeMillis()}" // Dispatchers.Main
        withContext(Dispatchers.IO) { // Dispatchers.IO for waiting in background
            delay(1000) // Wait 1 second
        }
    }
}
```

