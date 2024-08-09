# Android-Part3

===========
Parcelable
===========
NAGARRO, EXPEDIA
Android Parcelable implementation allows objects to read and write from Parcels which can contain flattened data inside message containers. Parcelable and Bundle objects are intended to be used across process boundaries such as with IPC/Binder transactions, between activities with intents, and to store transient state across configuration changes. The Bundle class is highly optimized for marshalling and unmarshalling using parcels.

======
To convert a Java object into Parcelable, then the best way to do so is by implementing the Parcelable interface and overriding the writeToParcel() methods in its own class. The first step is to override the writeToParcel() method and write all object members into parcel objects. The second is to create a static Parcelable.Creator object to de-serialize the Java object.

======
What's difference between Serializable and Parcelable?
Parcelable and Serialization are used for marshaling and unmarshaling Java objects
- In Parcelable, developers write custom code for marshalling and unmarshalling so it creates less garbage objects in comparison to Serialization. The performance of Parcelable over Serialization dramatically improves (around two times faster), because of this custom implementation.
- Serialization is a marker interface, which implies the user cannot marshal the data according to their requirements.
- In Serialization, a marshaling operation is performed on a Java Virtual Machine (JVM) using the Java reflection API. This helps identify the Java objects member and behavior, but also ends up creating a lot of garbage objects. Due to this, the Serialization process is slow in comparison to Parcelable.

======
For the specific case of savedInstanceState, the amount of data should be kept small because the system process needs to hold on to the provided data for as long as the user can ever navigate back to that activity (even if the activity's process is killed). We recommend that you keep saved state to less than 50k of data.

======
The Binder transaction buffer has a limited fixed size, currently 1MB, which is shared by all transactions in progress for the process. Since this limit is at the process level rather than at the per activity level, these transactions include all binder transactions in the app such as onSaveInstanceState, startActivity and any interaction with the system. When the size limit is exceeded, a TransactionTooLargeException is thrown.

===================
Volley vs Retrofit
===================
NAGARRO, URBANCLAP
- In terms of inbuilt types Volley have only 4 response types 
	-- StringRequest: This type of request converts the response into a String.
	-- JsonObjectRequest: This type of request and response is automatically converted into a JSONObject.
	-- JsonArrayRequest: This type of request and response is automatically converted into a JSONArray.
	-- ImageRequest: This type of request converts the response into a decoded bitmap.

- Retrofit have 6 types
	-- Boolean: Web API response needs to be a boolean.
	-- Integer: Web API response needs to be an integer. 
	-- Date: Web API response should be Long format date.
	-- String: Web API response needs to be in String format.
	-- Object: Web API response needs to be in Json object.
	-- Collections: Web API response needs to be in a String Format

- Volley has inbuilt image loading support whereas Retrofit does not provide such feature. Though other libraries like Picasso and glide can be used with Retrofit.
- Volley has flexible caching mechanism whereas Retrofit does not provide support for caching.
- Volley support retrying mechanism whereas Retrofit does not provide support for retrying.
- For post requests and multi-part uploads retrofit has full support whereas in volley the developer has to write extra code to add that feature.
- Retrofit is lot easier to configure

Neither Retrofit nor Volley is designed for this, and you will probably run into out-of-memory issues if you try. Both recommend using DownloadManager instead, which supports resuming and progress notifications. Both Volley and Retrofit supports request cancellation.

========
SECURITY
========
NAGARRO, HOME CREDIT
Always use a SSL connection between the client and the server.

====
If you thing using progaurd helps in encryption of the string API key. Not really. Proguard states in its FAQ that String encryption is not totally possible.

====
Secret Keys should not be stored in shared preferences without encrypting this data first because they can be extracted when performing a backup of your data. SharedPreferences can be easily accessed from the Emulator or any rooted device.
<application ...
    android:allowBackup="true">
</application>

====
HardCoded strings: An API key is much much more likely to be found in the assets or resources folder than in the decompiled code because it’s much easier to update xml than compiled code.

====
The Android Native Development Kit (NDK) allows you to write code in C/C++, and can be very useful when you’re trying to hide things like API keys. The good news is that NDK libraries can’t be decompiled making the information harder to find. The NDK compiled code can still be opened with a hexidecimal editor but by the nature of the API key they are harder to pick out in a hexidecimal editor.
https://medium.com/@abhi007tyagi/storing-api-keys-using-android-ndk-6abb0adcadad

====
Public/private API key exchange

====
Disallow access to your app's content providers. Use tag exported to false.

====
Ask for credentials before showing sensitive information. The Biometric library displays a system prompt, asking the user to log in using a biometric credential. The resulting consistency in dialog appearance creates a more trustworthy user experience.

====
Use WebView objects carefully: Whenever possible, load only white-listed content in WebView objects. In other words, the WebView objects in your app shouldn't allow users to navigate to sites that are outside of your control. In addition, you should never enable JavaScript interface support unless you completely control and trust the content in your app's WebView objects.

====
Store all private user data within the device's internal storage, which is sandboxed per app. Your app doesn't need to request permission to view these files, and other apps cannot access the files. As an added security measure, when the user un-installs an app, the device deletes all files that the app saved within internal storage.

====
Keep all the dependencies up to date. As if there is any issue, that would be fixed in the latest release. 
Never release the production apk without progaurd or R8. We can use Firebase database to access the API keys if user is getting logged in using Firebase login method.

====
For Google API keys which are to mentioned in the xml, for all those keys the keys has to be restricted for only our app by mentioning in console.google.com.

====
Always check what are you printing in the logcat. You can use internal storage for sensitive information. Don’t process any payments on a rooted device. Because the rooted device can change your code at runtime and alter the behavior of it.

====
Always protect your services and content provider using service. To prevent leaking your data make sure to also set the exported flag to false like this.
<provider
    android:name="ContentProvider"
    android:authorities="com.ahmedabdelmeged.contentprovider"
    android:exported="false" />

====
Encrypt data on External Storage and Check the validity of that data. One of the most popular encryption algorithms is AES, short for Advanced Encryption Standard, with a key size of 256 bits. Writing code to encrypt and decrypt your app’s data using the javax.cryptopackage, which is included in the Android SDK, can be confusing. Therefore, You can use third-party libraries, such as Facebook's Conceal library, which are usually much easier to work with.

====
Android keystore system
https://medium.com/@ericfu/securely-storing-secrets-in-an-android-application-501f030ae5a3

====
UrbanClap: Pulling mechanism in observer pattern
When implementing the Observer pattern, there are two main approaches to consider: the 'push' model and the 'pull' model. In the 'push' model, the subject (i.e. the Observable) sends the observer on notification all the data it will need. The observer doesn't need to query the subject for information. In the 'pull' model, the subject merely notifies the observer that something happened, and the observer queries the subject based to get the information it needs.
Push: The main advantage of the 'push' model is lower coupling between the observer and the subject. The observer doesn't need to know anything about the subject in order to query it. If it needed to, we'd need to do one of the following: A- do down-casting on the side of the observer in order to invoke class-specific get methods on the subject. This is bad. B- make the Observable interface more class-specific, offering specific get methods, thus making the relationship between the observer and subject less general and making things more coupled. By implementing the 'push' model, we avoid all of this. However the disadvantage is less flexibility: the subject may not always know what exact information the observers need in order to send it to them. This will often mean more specific Observer interfaces, such as AgeObserver that are notified when the 'age' of the subject is changed, and HeightObserver which are sent the current height of the subject on notification. This is one option. The other is the subject sending a whole lot of information encapsulated in an Info object of some sort and have the observers query it from there. But again, we can't be sure we're sending the correct info. So it's either this, or forcing the observers to implement more specific Observer interfaces, which tightens the coupling on the observer's side.
Pull: The observers would have to know things about the subject in order to query the right information, which leads A- to down-casting (ugly), or B- favorably to more specific Observable interfaces, that offer more specific access methods. For example AgeObservable offers a getAge() method. The advantage of this is more flexibility. Each observer can decide for itself what to query, without relying on the subject to send the correct information.

=====
UrbanClap: Lifecycle of view
This class represents the basic building block for user interface components. A View occupies a rectangular area on the screen and is responsible for drawing and event handling. View is the base class for widgets, which are used to create interactive UI components (buttons, text fields, etc.). The ViewGroup subclass is the base class for layouts, which are invisible containers that hold other Views (or other ViewGroups) and define their layout properties.
When a view is created, this is the order in which the following methods are called:
1. Constructor
	- CustomView(Context context) (if created pro-grammatically)
	- CustomView(Context context, AttributeSet attrs) (if created from xml)
2. onFinishInflate (assuming you used the xml constructor)
3. onAttachedToWindow
4. onMeasure
5. onSizeChanged
6. onLayout
7. onDraw

The earliest you can get the view’s measurements is in onMeasure. Before that the width and height are 0. However, the only thing you should be doing in onMeasure is determining the size of the view. This method gets called several times while the view is telling the parent how big it wants to be, but the parent is determining the actual final size.If you want to actually use the measured size for anything, the earliest place to do that is in onSizeChanged. It gets called whenever the view is created because the size is changing from 0 to whatever the size will be. Unlike onMeasure, onLayout is called only once during the traversal. So it is recommended to perform any complex calculations in this method. You can also access the size in onDraw with getMeasuredWidth() and getMeasuredHeight(). However, if you are using them to do any heavy calculations, it is better to do that beforehand. Generally speaking, try to keep as much out of onDraw as possible since it may be called multiple times. (It gets called whenever invalidate() gets called.)

invalidate() method used to simple redrawing view. While our view, for example, updates its text, color or touch interactivity. It means that view will only call onDraw() method once more to update its state. Tips: when dealing with animation, we need to call invalidate() to draw it. We need to call invalidate() every time new animated values comes out.

requestLayout() method, as you can see it will produce view update through its lifecycle just from onMeasure() method. This means that you will need it after your view updates. Since it changed its size, you need to measure it once again to draw it depending on the new size.

======
Difference between getHeight() and getMeasuredHeight: The size of a view is expressed with a width and a height. A view actually possess two pairs of width and height values. The first pair is known as measured width and measured height. These dimensions define how big a view wants to be within its parent (see Layout for more details.) The measured dimensions can be obtained by calling getMeasuredWidth() and getMeasuredHeight().
The second pair is simply known as width and height, or sometimes drawing width and drawing height. These dimensions define the actual size of the view on screen, at drawing time and after layout. These values may, but do not have to, be different from the measured width and height. The width and height can be obtained by calling getWidth() and getHeight().

=======
Detail lifecycle of view:
Constructor: Creating attributeSet. Create a new file and call it attrs.xml
public PageIndicatorView(Context context, AttributeSet attrs) {
    super(context, attrs);
    TypedArray typedArray = getContext().obtainStyledAttributes(attrs, R.styleable.PageIndicatorView);
    int count = typedArray.getInt(R.styleable.PageIndicatorView_piv_count,0);
    typedArray.recycle();
}
onAttachedToWindow: After parent view calls addView(View) that view will be attached to a window.  At this stage our view will know the other views that it is surrounded by. If your view is working with user’s other views located in same layout.xml it is good place to find them by id (which you can set by attributes) and save as a global reference (if needed).
onMeasure: Means that our custom view is on stage to find out it’s own size. It’s very important method, as for most cases you will need your view to have specific size to fit in your layout. While overriding this method, what you need to do this is to set setMeasuredDimension(int width, int height).
onLayout: This method, incorporates assigning a size and position to each of its children. Because of that, we are looking into a flat custom view (that extends a simple View) that does not have any children so there is no reason to override this method.
onDraw: That’s where the magic happens. Having both Canvas and Paint objects will allow you draw anything you need. A Canvas instance comes as onDraw parameter, it basically respond for drawing a different shapes, while Paint object defines that color that shape will get. Simply, Canvas respond for drawing an object, while Paint for styling it. And it used mostly everywhere whether it is going to be a line, circle or rectangle. While making a custom view, always keep in mind that onDraw calls lots of time, like really a lot. While having some changes, scrolling, swiping your will be redrawn. So that’s why even Android Studio recommend to avoid object allocation during onDraw operation, instead to create it once and reuse further on.

invalidate() method used to simple redrawing view. While your view for example updates its text, color or touch interactivity. It means that view will only call onDraw() method once more to update its state.
requestLayout() method, as you can see will produce view update through its lifecycle just from onMeasure() method. And what it means that you will need it while after your view updates, it changed it’s size and you need to measure it once again to draw it depending on new size.

Animation: Animations in custom view is frame by frame process. It means that if you for example want to make a circle radius animate from smaller to bigger you will need to increase it one by one and after each step call invalidate() to draw it. Your best friend in custom view animations is ValueAnimator. This class will help you to animate any value from start to the end with even Interpolator support (if you need).
ValueAnimator animator = ValueAnimator.ofInt(0, 100);
animator.setDuration(1000);
animator.setInterpolator(new DecelerateInterpolator());
animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
  public void onAnimationUpdate(ValueAnimator animation) {
    int newRadius = (int) animation.getAnimatedValue();
  }
});
animator.start();
https://proandroiddev.com/android-draw-a-custom-view-ef79fe2ff54b

=====
Zoom-in Zoom-out
Extending the onTouchListener and creating the instance of ScaleGestureDetector which helps you to detect how much scaling of image user has done. Then overriding the onMeaure method of the ImageView on which we have implemented onTouchListener. We use the scale value to get the width and height of the new zoomed image to be shown in our imageView.

=====
IllegalStateException : Cannot perform this action after onSaveInstanceState().
Consider you are sending a network request from an activity and want to place a fragment using FragmentTransaction.commit() once the network request is successful. Suppose user presses home button before the request returns successfully, this will lead to onSaveInstanceState of the Activity being called and hence when you try to commit the fragment transaction after this the app will crash with the above error.
Issue: The main root cause of this exception is that fragment transactions are not allowed after the OnSaveInstanceState of the activity is called. Post honeycomb Activities are considered killable only after onStop() which means onSaveInstanceState will be called before onStop.

=====
When you commit a Fragment(using fragmentTransaction.commit) the transaction wont happen immediately it is rather scheduled on the worker thread whenever the thread has time. You wont encounter this exception if you commit a FragmentTransaction in onCreate or in response to a user click but when you try to do this after lifecycle callbacks like onPause(),onSaveInstanceState() chances are high that you will encounter an exception. In simple terms this could be understood as fragment should only be committed when the activity is in foreground else you will encounter IllegalStateException.

=====
Solution: 
commitAllowStateLoss: This does the same this what commit does but even if the state is lost this wont throw an exception. So basically we are not solving the issue but rather hiding it. Android developer docs describe this as dangerous since it might lead to unexpected UI behavior where the fragment commit is lost if the activity is restored again. But this will behaviore handy if the UI behavior doesn’t matter.
There is another solution by using the two booleans given in the below link
https://androidclarified.com/illegalstateexception-cannot-perform-this-action-after-onsaveinstancestate/

=====
How to check if the keyboard is open or not: There is no direct way, but we can use the global layout listener of the root layout of activity/fragment
boolean isKeyboardShowing = false;
void onKeyboardVisibilityChanged(boolean opened) {
    print("keyboard " + opened);
}
// ContentView is the root view of the layout of this activity/fragment    
contentView.getViewTreeObserver().addOnGlobalLayoutListener(
    new ViewTreeObserver.OnGlobalLayoutListener() {
    @Override
    public void onGlobalLayout() {
        Rect r = new Rect();
        contentView.getWindowVisibleDisplayFrame(r);
        int screenHeight = contentView.getRootView().getHeight();

        // r.bottom is the position above soft keypad or device button.
        // if keypad is shown, the r.bottom is smaller than that before.
        int keypadHeight = screenHeight - r.bottom;
        Log.d(TAG, "keypadHeight = " + keypadHeight);

        if (keypadHeight > screenHeight * 0.15) { // 0.15 ratio is perhaps enough to determine keypad height.
            // keyboard is opened
            if (!isKeyboardShowing) {
                isKeyboardShowing = true
                onKeyboardVisibilityChanged(true)
            }
        }
        else {
            // keyboard is closed
            if (isKeyboardShowing) {
                isKeyboardShowing = false
                onKeyboardVisibilityChanged(false)
            }
        }
    }
});

=======
TOOLS
=======
Tools attributes are special XML attributes that enable not only design-time features (such as which layout should be drawn inside the fragment), as well as compile-time behavior (such as suppressing lint warnings).

=====
These attributes are used solely by Android Studio and removed by build tools at build time of the app. So, using these attributes has no impact on your APK size or runtime behavior. To use these attributes, tools namespace should be added to the root element of XML file, as shown below:
<RootTag xmlns:android=”http://schemas.android.com/apk/res/android"
 	xmlns:tools=”http://schemas.android.com/tools" >

===================
Android App Bundle
===================
App Bundle: It is a new upload format that includes all your app’s compiled code and resources, but defers APK generation and signing to Google Play. But the contents like dex file, native libraries, resources, assets, android manifest file are present in the app bundle. Format is aab. Android app bundle. 

======
Why should we use this new format?
- Dynamic Delivery : Google Play’s new app serving model, called Dynamic Delivery, uses your app bundle to generate and serve optimized APKs for each user’s device configuration, so they download only the code and resources they need to run your app. For example, you don’t need other languages strings if you have set English as your default language.

- No need to manually manage multiple APKs : You no longer have to build, sign, and manage multiple APKs to support different devices, and users get smaller, more optimized downloads. For example, now you don’t have to create multiple APKs for devices with different screen resolutions.

- Dynamic Feature Module : These modules contain features and assets that you can decide not to include when users first download and install your app. Using the Play Core Library, your app can later request to download those modules as dynamic feature APKs. For example, video calling feature and camera filters can be downloaded later on demand.

- Reduced APK size : Using Split APK mechanism(explained later), Google Play can break up a large app into smaller, discrete packages that are installed on a user’s device as required. On average, apps published with app bundles are 20% smaller in size.

======
Splitting the APKs
- Base APK : This APK contains code and resources that all other split APKs can access and provides the basic functionality for your app. When the user downloads the app, they always get this APK.

- Configuration APKs : Each of these APKs includes only native libraries and resources for a given device configuration — screen density, language or CPU architecture. When a device downloads a base or dynamic feature APK, it downloads only the libraries and resources it needs. Add the following code in android {} block to enable language, density, and abi configuration splits
bundle {
   language {
       enableSplit = true
   }
   density {
       enableSplit = true
   }
   abi {
       enableSplit = true
   }
}

- Dynamic Feature APKs : Each of these APKs contain code and resources that are not required when your app is first installed, but may be downloaded and installed later.

========
Context
========
The context is the current state of the application/object. It lets newly-created objects understand what has been going on. Typically you call it to get information regarding another part of your program (activity and package/application). It is a handle to the system, it provides services like resolving resources, obtaining access to databases and preferences. An Android app has activities. It’s like a handle to the environment your application is currently running in. The activity object inherits the Context object. It allows access to application specific resources and class and information about the application environment. Wrong use of Context can easily lead to memory leaks in an android application.

======
Application Context: It is an instance which is the singleton and can be accessed in an activity via getApplicationContext(). This context is tied to the lifecycle of an application. The application context can be used where you need a context whose lifecycle is separate from the current context or when you are passing a context beyond the scope of an activity. Example: If you have to create a singleton object for your application and that object needs a context, always pass the application context. If you pass the activity context here, it will lead to the memory leak as it will keep the reference to the activity and activity will not be garbage collected. In case, when you have to initialize a library in an activity, always pass the application context, not the activity context. You only use getApplicationContext() when you know you need a Context for something that may live longer than any other likely Context you have at your disposal.

======
Activity Context: This context is available in an activity. This context is tied to the lifecycle of an activity. The activity context should be used when you are passing the context in the scope of an activity or you need the context whose lifecycle is attached to the current context. Example Use: If you have to create an object whose lifecycle is attached to an activity, you can use the activity context.

======
getContext() in ContentProvider: This context is the application context and can be used similar to the application context. This can be accessed via getContext() method.

======
When not to use getApplicationContext(): It’s not a complete Context, supporting everything that Activity does. Various things you will try to do with this Context will fail, mostly related to the GUI. It can create memory leaks if the Context from getApplicationContext() holds onto something created by your calls on it that you don't clean up. With an Activity, if it holds onto something, once the Activity gets garbage collected, everything else flushes out too. The Application object remains for the lifetime of your process.

======
The Rule of Thumb: In most cases, use the Context directly available to you from the enclosing component you’re working within. You can safely hold a reference to it as long as that reference does not extend beyond the lifecycle of that component. As soon as you need to save a reference to a Context from an object that lives beyond your Activity or Service, even temporarily, switch that reference you save over to the application context.

=================
Handling Bitmaps
=================
There are several libraries that follow best practices for loading images. You can use these libraries in your app to load images in the most optimized manner. We recommend the Glide library, which loads and displays images as quickly and smoothly as possible. Other popular image loading libraries include Picasso from Square and Fresco from Facebook. These libraries simplify most of the complex tasks associated with bitmaps and other types of images on Android.

======
BitmapFactory: Creates Bitmap objects from various sources, including files, streams, and byte-arrays.
The BitmapFactory class provides several decoding methods (decodeByteArray(), decodeFile(), decodeResource(), etc.) for creating a Bitmap from various sources. Choose the most appropriate decode method based on your image data source. These methods attempt to allocate memory for the constructed bitmap and therefore can easily result in an OutOfMemory exception.

======
Each type of decode method has additional signatures that let you specify decoding options via the BitmapFactory.Options class. Setting the inJustDecodeBounds property to true while decoding avoids memory allocation, returning null for the bitmap object but setting outWidth, outHeight and outMimeType. This technique allows you to read the dimensions and type of the image data prior to construction (and memory allocation) of the bitmap.

BitmapFactory.Options options = new BitmapFactory.Options();
options.inJustDecodeBounds = true;
BitmapFactory.decodeResource(getResources(), R.id.myimage, options);
int imageHeight = options.outHeight;
int imageWidth = options.outWidth;
String imageType = options.outMimeType;

======
Load a Scaled Down Version into Memory: Now that the image dimensions are known, they can be used to decide if the full image should be loaded into memory or if a sub-sampled version should be loaded instead. Here are some factors to consider:
1) Estimated memory usage of loading the full image in memory.
2) Amount of memory you are willing to commit to loading this image given any other memory requirements of your application.
3) Dimensions of the target ImageView or UI component that the image is to be loaded into.
4) Screen size and density of the current device.

public static int calculateInSampleSize(BitmapFactory.Options options, int reqWidth, int reqHeight) {
    // Raw height and width of image
    final int height = options.outHeight;
    final int width = options.outWidth;
    int inSampleSize = 1;

    if (height > reqHeight || width > reqWidth) {

        final int halfHeight = height / 2;
        final int halfWidth = width / 2;

        // Calculate the largest inSampleSize value that is a power of 2 and keeps both
        // height and width larger than the requested height and width.
        while ((halfHeight / inSampleSize) >= reqHeight && (halfWidth / inSampleSize) >= reqWidth) {
            inSampleSize *= 2;
        }
    }
    return inSampleSize;
}

public static Bitmap decodeSampledBitmapFromResource(Resources res, int resId, int reqWidth, int reqHeight) {

    // First decode with inJustDecodeBounds=true to check dimensions
    final BitmapFactory.Options options = new BitmapFactory.Options();
    options.inJustDecodeBounds = true;
    BitmapFactory.decodeResource(res, resId, options);

    // Calculate inSampleSize
    options.inSampleSize = calculateInSampleSize(options, reqWidth, reqHeight);

    // Decode bitmap with inSampleSize set
    options.inJustDecodeBounds = false;
    return BitmapFactory.decodeResource(res, resId, options);
}

=======
If you want to fetch the size
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
    // From Android 4.4 (KitKat) onward we can re-use if the byte size of
    // the new bitmap is smaller than the reusable bitmap candidate allocation byte count.
    int width = targetOptions.outWidth / targetOptions.inSampleSize;
    int height = targetOptions.outHeight / targetOptions.inSampleSize;
    int byteCount = width * height * getBytesPerPixel(candidate.getConfig());
    return byteCount <= candidate.getAllocationByteCount();
}
	
// A helper function to return the byte usage per pixel of a bitmap based on its configuration.
static int getBytesPerPixel(Config config) {
    if (config == Config.ARGB_8888) {
        return 4;
    } else if (config == Config.RGB_565) {
        return 2;
    } else if (config == Config.ARGB_4444) {
        return 2;
    } else if (config == Config.ALPHA_8) {
        return 1;
    }
    return 1;
}

======
How two applications can share data?
- Content providers
- The Android system provides signature-based permissions enforcement, so that an application can expose functionality to another application that is signed with a specified certificate. By signing multiple applications with the same certificate and using signature-based permissions checks, your applications can share code and data in a secure manner.

=======================
How Android draws view
=======================
When a user brings an Android view into focus, the Android framework directs the view to draw itself. This drawing process comprises 3 phases:
- Measure: The system completes a top-down traversal of the view tree to determine how large each ViewGroup and View element should be. When a ViewGroup is measured, it also measures its children.
- Layout: Another top-down traversal occurs, with each ViewGroup determining the positions of its children using the sizes determined in the measure phase.
- Draw: The system performs yet another top-down traversal. For each object in the view tree, a Canvas object is created to send a list of drawing commands to the GPU. These commands include the ViewGroup and View objects' sizes and positions, which the system determined during the previous 2 phases.

Each phase within the drawing process requires a top-down traversal of the view tree. Therefore, the more views you embed within each other (or nest) into the view hierarchy, the more time and computation power it takes for the device to draw the views. By keeping a flat hierarchy in your Android app layouts, you can create a fast and responsive user interface for your app. Nested hierarchies can adversely affect performance.

======
Why Constraint layout is preferred upon other layouts:
Systrace automatically highlights the (numerous) performance problems with this layout, as well as suggestions for fixing them. Triggering that many expensive measure and layout phases is far from ideal; such a large amount of drawing activity could result in skipped frames that users notice. We can conclude that the layout has poor performance due to the nested hierarchy as well as the characteristic of RelativeLayout, which measures each of its children twice. ConstraintLayout allows you to build complex layouts without having to nest View and ViewGroup elements. It has flat hierarchy.

======
- LinearLayout means you can align views one by one (vertically/ horizontally).
- RelativeLayout means based on relation of views from its parents and other views.
- ConstraintLayout is similar to a RelativeLayout in that it uses relations to position and size widgets, but has additional flexibility and is easier to use in the Layout Editor.
- FrameLayout to load child one above another, like cards inside a frame, we can place one above another or anywhere inside the frame.

============
APP STARTUP
============
General:
- Android starts a new Linux process for an app when an app component (Activity, Service, Content Provider, Broadcast Receiver) needs to be started and the app does not have a running process yet
- All app components of the same app run in the same process and thread (main thread) → default behavior
- If an app component starts and there already exists a process for that app, then the new component is started within that already existing process

App startup sequence:
- Start Linux process
- Create runtime: Dalvik or ART (Android Runtime)
- Create Application instance (android.app.Application.onCreate() is called)
- Create the component for the application (e.g. Activity, Service, …) - It is the component that needs to be executed and triggered the startup

======
App launch can take place in one of three states, each affecting how long it takes for your app to become visible to the user: cold start, warm start, or hot start. In a cold start, your app starts from scratch. In the other states, the system needs to bring the running app from the background to the foreground. 

======
Cold start: refers to an app’s starting from scratch: the system’s process has not, until this start, created the app’s process. Cold starts happen in cases such as your app’s being launched for the first time since the device booted, or since the system killed the app.
At the beginning of a cold start, the system has three tasks. These tasks are:
- Loading and launching the app.
- Displaying a blank starting window for the app immediately after launch.
- Creating the app process.

As soon as the system creates the app process, the app process is responsible for the next stages:
- Creating the app object.
- Launching the main thread.
- Creating the main activity.
- Inflating views.
- Laying out the screen.
- Performing the initial draw.
Once the app process has completed the first draw, the system process swaps out the currently displayed background window, replacing it with the main activity. At this point, the user can start using the app.

======
Hot start: of your application is much simpler and lower-overhead than a cold start. In a hot start, all the system does is bring your activity to the foreground. If all of your application’s activities are still resident in memory, then the app can avoid having to repeat object initialization, layout inflation, and rendering. However, if some memory has been purged in response to memory trimming events, such as onTrimMemory(), then those objects will need to be recreated in response to the hot start event. A hot start displays the same on-screen behavior as a cold start scenario: The system process displays a blank screen until the app has finished rendering the activity.

======
Warm start: encompasses some subset of the operations that take place during a cold start; at the same time, it represents less overhead than a hot start. There are many potential states that could be considered warm starts. For instance: 
- The user backs out of your app, but then re-launches it. The process may have continued to run, but the app must recreate the activity from scratch via a call to onCreate(). 
- The system evicts your app from memory, and then the user re-launches it. The process and the activity need to be restarted, but the task can benefit somewhat from the saved instance state bundle passed into onCreate().

======
How does findViewById works: Every time we try to find the reference to an id, android system has to go through view heirarchy and find it at run time. There may be so many layouts, so going through the heirarchy every time during runtime is not a good idea. We should use databinding for the same. We create a binding object which contains references to each view for that layout. Once the binding object has been created, all the components of the app can access the views and other data through binding object. So no need to search again and again in heirarchy. This improves the performance of the app. It also eliminites findViewById makes code concise, makes code easier to read and maintain. This data binding object creating happens in the compile time. Therefore if there is any error, we can recognize during the compile time.
1) Update the values automatically.(doesn’t have to keep track of all the ways a value can be updated)
2) Very effective for UI Testing.
3) More readable code.
4) More maintainable code.
5) Faster development times.
6) Faster execution times.
7) Well suited for MVVM and MVI architectures.
8) Errors can be found during the compile time.

======
Viewmodel is designed to store and manage the UI-related data. Whenever there is configuration change, the data is saved and will be reloaded. We usually create one view model for an activity or fragment. Somtimes two or more activities or fragments. View model creates in the memory, when the activity creates, it lives until activity cleared from the memory. Therfore view model can hold values belong to the activity.

======
A ViewModel's onCleared() is called when the app is put into the background and the app process is killed in order to free up the system's memory.

======
What are the configurational changes?
- Screen rotations
- Keyboard changes
- Language changes
- Enabling multiwindow mode

======
From where do we observe LiveData?
- LiveData is a lifecycle-aware observable data holder class. However, in android we have only 3 app components with lifecycles. Activities, fragments and services. So, from activities, fragments and services we can observe LiveData.

======
RxJava Vs LiveData.
- RxJava is not a  lifecycle aware component. So, data stream does not go off,  when activity, fragment or service becomes inactive. As a result of that, memory leaks or crashes can happen. Therefore, we have to write codes to dispose them manually. But, on the other hand, Android LiveData aware of lifecycle status changes. And, they clean up themselves when their associated lifecycle is destroyed.

======
MutableLiveData vs LiveData
MutableLiveData class is a subclass of LiveData class. In other words, MutableLiveData child class has created by extending the parent  LiveData class. A MutableLiveData instance can do everything a LiveData instnce can do and more. Data in a LiveData object are only readable. We cannot update those values. But, in the other hand, a Mutable LiveData object allows us to change(update) its values. So, When we are creating our own live data(mostly in ViewModels), we define them as MutableLiveData. But, when we are getting live data from other libraries such as Room and Retrofit we get them as LiveData. We can easily transfer values between those two formats.

======
Official Statement: ViewModel is a class that is responsible for preparing and managing the data for an Activity or a Fragment. It also handles the communication of the Activity / Fragment with the rest of the application (e.g. calling the business logic classes). A ViewModel is always created in association with a scope (a fragment or an activity) and will be retained as long as the scope is alive. E.g. if it is an Activity, until it is finished. In other words, this means that a ViewModel will not be destroyed if its owner is destroyed for a configuration change (e.g. rotation). The new owner instance just re-connects to the existing model.

======
The Navigation component consists of three key parts that are described below:
1) Navigation graph: An XML resource that contains all navigation-related information in one centralized location. This includes all of the individual content areas within your app, called destinations, as well as the possible paths that a user can take through your app.
2) NavHost: An empty container that displays destinations from your navigation graph. The Navigation component contains a default NavHost implementation, NavHostFragment, that displays fragment destinations.
3) NavController: An object that manages app navigation within a NavHost. The NavController orchestrates the swapping of destination content in the NavHost as users move throughout your app
