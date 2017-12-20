# 1. Reduce application size

Remove all unused resources and library dependencies. Optimize all resource files (e.g. splash screens and static images).
Enable Proguard to reduce the size of the APK. It strips parts of the React Native Java bytecode (and its dependencies) that your app is not using.
To enable Proguard, edit **android/app/build.gradle:**

```javascript
def enableProguardInReleaseBuilds = true
```


# 2. Use unique key property in lists
Provide key property when rendering list items in order to help React to perform change detection more efficiently. Otherwise, the JS thread will send unnecessary data to shadow thread through RN bridge which requires serialization/deserialization and rerendering UI components in the main thread.


# 3. Use _shouldComponentUpdate()_ lifecycle method
Stop React reconciliation phase and prevent component from rendering if certain props are not changed.


# 4. Optimize requests for cached data
Combine multiple requests for cached data on the disc (e.g. AsyncStorage) into one. Instead of accessing multiple keys from AsyncStorage you can grab whole object at once. Startup phase will be faster if those requests are batched.


# 5. Use FlatList instead of ListView
When scrolling down a long list, the memory footprint increases linearly and eventually exhausts all available memory. The main advantage of FlatList is that is has nearly constant memory usage for any number of rows.


# 6. Use native navigation instead of Javascript navigation
When using Javascript navigation all transitions and animations are being executed in Javascript thread. If there’s a content which needs to be rendered on the page (long lists, maps, animations...), while transitions is executing, the application will not be able to respond and frames will be lost, because Javascript is single threaded.
Currently available native solutions are **[React Native Navigation](https://github.com/wix/react-native-navigation)** by Wix and **[Native Navigation](https://github.com/airbnb/native-navigation)** by AirBnb. **[React Navigation](https://reactnavigation.org/)** is the official solution from React team based on Javascript.


# 7. Use useNativeDriver for animations
Native driver transfers the animation execution to main thread, instead of Javascript thread. In that case Javascript operations can be performed independently. If your user experience is suffering way more than expected, then you should better stick with Layout Animation. Layout Animation is used e.g., when animating in a modal (sliding down from top and fading in a translucent overlay) while doing something else at the same time (while initializing and perhaps receiving responses for several network requests, rendering the contents of the modal, and updating the view where the modal was opened from)


# 8. When resizing the image use transform: [{scale}]
This process consumes a lot of memory because every time a user adjust the width or height of an image it is re-cropped and scaled from the original image size. When resizing the image (tap to zoom in etc.) use transform: [{scale}] style property to animate the size or, even more efficient, implement a custom **[FadeInImage](https://gist.github.com/sharathprabhal/a54565ee1ce42d332f09)** component to display a tiny shade


# 9. Convert images into WebP Format
It can speed out your image loading time to 28%.


# 10. Image placeholders optimization
In order to display an image shell while image is not yet loaded from the server use **[defaultSource](https://facebook.github.io/react-native/docs/image.html#defaultsource)** property as image placeholders or **[react-native-image-placeholder](https://www.npmjs.com/package/react-native-image-placeholder)** npm package.


# 11. Use progressive image loading for larger images
Display small thumbnail first (easy to fetch from the server) and replace it with real image once the image is being loaded. Code to achieve this: **[ProgressiveImage.js](https://gist.github.com/alinz/d10a92d3dd0192a3df1197f9393de7eb)**


# 12. Avoid memory leaks caused by images
If there’s a problem with memory leak because of images try to reduce the image size or use JPEG instead of PNG because downsampling is disabled for PNGs in React Native, since it is still experimental.


# 13. Cleanup all _console.log()_ statements
Cleanup all console.log() statements using **[Babel plugin](https://babeljs.io/docs/plugins/transform-remove-console)**.


# 14. Use initial loading view at startup
When launching React Native app it takes time to load a JavaScript Context and do the initial rendering. It results in a white screen splash. Use initial loading view at startup to avoid this: **[React Native Initial Loading View](https://medium.com/rendez-voo/react-native-initial-loading-view-e58f919fa31f)**.


# 15. Avoid memory leaks caused by big data
If your application uses a lot of memory it could lead to memory leak. Possible solution is to set

```javascript
<application android:largeHeap="true" 
```

in _“android/app/src/main/AndroidManifest.xml”_ file.
