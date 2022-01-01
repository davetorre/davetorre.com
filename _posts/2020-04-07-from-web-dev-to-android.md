---
layout: post
title: "From Web Dev to Android"
date: 2020-04-07
---

Most of my software experience has been in developing web applications that use various front-end frameworks. By now I've come to expect these frameworks to help me do certain things. These include breaking down complex UI screens into smaller sections, passing data into those sections to be shown on screen, and navigating from one screen to the next.

Recently I worked on an Android project and found myself wanting the same. With this post, I hope to give you a taste of Android by way of comparison with React, and to direct you toward topics you might want to check out as a new Android developer.

## Small View + Small View = Big View

Large UI screens can become difficult to manage, so I like to break them down into smaller pieces that can be composed together.

In React, once you've defined a Component, you can just use it within another component's `render()` function.

```javascript
class Greeting extends React.Component {
  render() {
    return (
      <p>Hi there</p>
    );
  }
}

class Home extends React.Component {
  render() {
    return (
      <div>
        <Greeting />
      </div>
    );
  }
}
```

In Android you can do this with Fragments, which are similar to React Components, and a FragmentManager. Fragment UI elements are laid out in XML layout files, and Fragments have lifecycle hooks, like `onViewCreated(...)`, where you can connect to those elements. In the example below, the HomeFragment uses a FragmentManager and a GreetingFragment to fill in the `LinearLayout` element with id `greeting_container`.

```xml
<!-- greeting_fragment.xml -->
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">
    <TextView
            android:text="Hi there"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
</LinearLayout>
```
```kotlin
// GreetingFragment.kt
class GreetingFragment : Fragment() {
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.greeting_fragment, container, false)
    }
}
```
```xml
<!-- home_fragment.xml -->
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">
    <LinearLayout
            android:id="@+id/greeting_container"
            android:layout_width="match_parent"
            android:layout_height="wrap_content">
</LinearLayout>
```
```kotlin
// HomeFragment.kt
class HomeFragment : Fragment() {
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.home_fragment, container, false)
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        childFragmentManager.beginTransaction()
            .replace(R.id.greeting_container, GreetingFragment())
            .commit()
    }
}
```

FYI, `childFragmentManager` (or `getChildFragmentManager()`) is a getter that is present on all Fragments. It returns a FragmentManager that is meant for placing and managing other Fragments inside a given Fragment.

## Passing Data Into Views

Because we often want UIs to show different information depending on the user, I like to think of them as functions that accept data specific to each user as input and return the personalized UI as output.

With React, you can pass data directly into your components as Props, as shown here.

```javascript
class City extends React.Component {
  render() {
    return (
      <p>City: {props.name}</p>
    );
  }
}

// within another component
<City name="Chicago" />
```

Not so with Android. You could try, but the OS might reload your Fragment while someone is using the app. When the Fragment is reloaded, its constructor will not be called with the same data you originally passed in.

To pass data into a Fragment safely, you can store the data on the `arguments` variable in the form of a `Bundle` object, as shown below. It's common to keep the arguments bundle code inside a helper function on the Fragment class's `companion object`, which is similar to defining a static method on a class in Java. I first saw this in the [Big Nerd Ranch Android book](https://www.bignerdranch.com/books/android-programming-the-big-nerd-ranch-guide-4th/). The code uses [Kotlin's `apply` function](https://kotlinlang.org/docs/reference/scope-functions.html). It allows you to initialize an object and call functions or set variables on it in one step.

```xml
<!-- city_fragment.xml -->
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">
    <TextView
            android:id="@+id/name"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
</LinearLayout>
```
```kotlin
// CityFragment.kt
class CityFragment : Fragment() {
    companion object {
        fun newInstance(name: String): CityFragment {
            val args = Bundle().apply {
                putString("name", name)
            }
            return CityFragment().apply {
                arguments = args
            }
        }
    }

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.city_fragment, container, false)
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        name.text = "City: " + arguments?.getString("name") as String
    }
}
```
```kotlin
// initializing the fragment elsewhere
CityFragment.newInstance("Chicago")
```

To pass data into a list of child components within a parent in React, you might map over your list of data and initialize a new Component for each item like this.

```javascript
const names = ["Chicago", "Los Angeles"];
const cities = names.map((name) =>
  <li><City name={name} /></li>
);
class Locations extends React.Component {
  render() {
    return (
      <div>
        <ul>{cities}</ul>,
      </div>
    );
  }
}
```

Android has specific abstractions for showing lists of Fragments depending on how you want the user to interact with them. These include [RecyclerView](https://developer.android.com/guide/topics/ui/layout/recyclerview) and [ViewPager](https://developer.android.com/training/animation/screen-slide).

## Navigation

While it's totally possible to build a single page web app that has only one screen, we often want to click links that take us to other screens in the app. In React we might use [React Router](https://github.com/ReactTraining/react-router) for this. To get similar behavior on Android, I've found the [Navigation component](https://developer.android.com/guide/navigation/) helpful.

The Navigation component is a library that lets you lay out the successions of Fragments in your app visually, connecting one to the next by clicking and dragging your mouse. The file that keeps track of the Fragment connections is called a NavGraph and is stored as an XML file. You can use references to these connections in conjunction with a NavController object inside your Fragments to perform the navigations.

In the code below, `findNavController()` is a function included in the library that returns a reference to the NavController associated with the NavGraph you set up. `setOnClickListener(...)` is a function on UI elements that lets you register a callback function to be invoked whenever someone clicks the element. Here the callback function first gets the NavController and then calls its `navigate` function with a reference to the navigation we want to perform, moving from the HomeFragment to the HelpFragment.

```xml
<!-- home_fragment.xml -->
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">
    <Button
            android:id="@+id/help_button"
            android:layout_width="match_parent"
            android:layout_height="wrap_content">
</LinearLayout>

```
```kotlin
// HomeFragment.kt
class HomeFragment : Fragment() {
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.home_fragment, container, false)
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        help_button.setOnClickListener {
            findNavController().navigate(R.id.action_homeFragment_to_helpFragment)
        }
    }
}
```

React Router can also help you set up deep links into your app, and to build always-present navigation bars that hold links to pages on your site. You can read about defining deep links into navigation graphs using Android's NavigationComponent [here](https://developer.android.com/guide/navigation/navigation-deep-link). For always-present navigation links, you might use the [App Bar](https://developer.android.com/training/appbar) and [BottomNavigationView](https://material.io/develop/android/components/bottom-navigation-view/) abstractions.

## Conclusion

Hopefully this post has made Android development a little less scary. I left out a lot of information here. Getting started with Android Studio, deploying an app to a device, learning about Activity/Fragment lifecycles, and managing app state with ViewModels come to mind. Android development libraries and best practices seem to change quickly so I don't mean to make this environment sound easy. But for me it's been helpful to realize that, while some new technology might be different from those I know, it might also solve some of the same problems in similar ways.

<style>
.language-javascript span.err {
    color: rgb(41, 50, 60) !important;
    background-color: rgba(0,0,0,0) !important;
}
.language-kotlin span.err {
    color: rgb(41, 50, 60) !important;
    background-color: rgba(0,0,0,0) !important;
}
</style>
