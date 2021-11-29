<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  
  <link rel="stylesheet" href="https://stackedit.io/style.css" />
</head>

<body class="stackedit">
  <div class="stackedit__html"><h1 id="delivery-solutions---proximity-sdk">Delivery solutions - Proximity SDK</h1>
<p>Android Native Library for fetching background location.</p>
<h1 id="installation">Installation</h1>
<ul>
<li>By using offline .aar file</li>
</ul>
<blockquote>
<p>You can download the aar file offline and add it to your project.</p>
</blockquote>
<pre><code>Project structure -&gt; Dependency -&gt; App Module -&gt; Click + icon -&gt; Add path to your .aar file -&gt; click apply -&gt; ok
</code></pre>
<blockquote>
<p>Android studio automatically add the dependency for your local aar file in grade and sync your project. You are ready to explore with Proximity SDK</p>
</blockquote>
<h2 id="permissions-services-and-receivers">Permissions, Services and Receivers</h2>
<ul>
<li>
<p>RUN-TIME PERMISSIONS</p>
<blockquote>
<p>Add these permission to applications Mainfest file.<br>
Your application should need these permissions enabled by the user.</p>
</blockquote>
<ul>
<li>android.permission.INTERNET</li>
<li>android.permission.ACCESS_FINE_LOCATION</li>
<li>android.permission.ACCESS_COARSE_LOCATION</li>
<li>android.permission.FOREGROUND_SERVICE</li>
<li>android.permission.ACCESS_BACKGROUND_LOCATION</li>
<li>android.permission.ACTIVITY_RECOGNITION</li>
</ul>
<h3 id="android.permission.internet">1.1 android.permission.INTERNET</h3>
<p>Internet permission is need to call external Api’s and to sync data with delivery solution.</p>
<h3 id="android.permission.access_fine_location--android.permission.access_coarse_location">1.2 android.permission.ACCESS_FINE_LOCATION &amp; android.permission.ACCESS_COARSE_LOCATION</h3>
<p>Inorder to access location of the device, ProximitySdk will need these permission enabled by the user.</p>
<h3 id="android.permission.foreground_service">1.3 android.permission.FOREGROUND_SERVICE</h3>
<p>Proximity SDK creates a forground service to access location from the background. Even if user kills the application from background, SDK starts a foreground service with notification that helps to access the location from the background.</p>
<h3 id="android.permission.access_background_location">1.4 android.permission.ACCESS_BACKGROUND_LOCATION</h3>
<p>Users should provide “All the time” Permission access.<br>
By this permission your application can access location from background even if the user is not using your application.</p>
<h3 id="android.permission.activity_recognition">1.5 android.permission.ACTIVITY_RECOGNITION</h3>
<p>Your application will get callbacks on Users Activity like { <strong>‘Still’</strong>,   <strong>‘In-Vehicle’</strong>, <strong>‘Walking’</strong>, <strong>‘On-Bicycle’</strong>, <strong>‘Running’</strong>}</p>
</li>
</ul>
<h2 id="forground-service.">Forground service.</h2>
<blockquote>
<p>Copy and paste below service into your manifest file.</p>
</blockquote>
<pre><code>&lt;service android:name="co.deliverysolutions.proximity.location.LocationService"  
  android:enabled="true"  
  android:exported="false"/&gt;
</code></pre>
<h2 id="user-activity-receiver">User Activity Receiver</h2>
<blockquote>
<p>Copy and paste this receiver code to your app Manifest file to access user-activity</p>
</blockquote>
<pre><code>&lt;receiver android:name="co.deliverysolutions.proximity.UserActivity$ActivityTransitionReceiver"  
  android:exported="false"  
 android:permission="com.google.android.gms.permission.ACTIVITY_RECOGNITION"/&gt;
</code></pre>
<h2 id="register-using-proximitysdk">Register using ProximitySDK</h2>
<blockquote>
<p>Your application should register with ProximitySDK before starting your location updates. You can register using your Organisation ID provided by Proximity SDK.<br>
Refer below code for register</p>
</blockquote>
<pre><code>lateinit var locationWrapper: ProximitySDK //global variable

proximitySDK.register(organisationID,  
 object : ProximitySDK.CallBacks{ 
       override fun success(s: String) {  
	          // you can receive the accessToken from proximity server for reference.
	      }  
       override fun error(e: String) {  
              // If sdk encountered any error you will get it here.
          }  
  }
</code></pre>
<p>You should call this register method before calling :startLocationUpdates.</p>
<h2 id="user-info">User Info</h2>
<p>User information should be send as a parameter, when you call <strong>startLocaitonUpdates</strong> method. You can make use of <strong>UserInfo</strong> model from Proximity SDK. You can find the sample code below.</p>
<pre><code>var userInfo = UserInfo(  
    "Black",           //vehicle color  
    false,             //is picking by someone else  
    "+917734777737",   //contact number  
    "A1",              //parking slot  
    "2nt Floor",       //description  
    "KA 12 AY 2343",   //vehicle number  
    "Roy",             //user name  
    "Toyota",          //vehicle make  
    "Sedan",           //vehicle type  
)
</code></pre>
<h2 id="register-for-location-updates">Register for location updates</h2>
<pre><code>var orderID = "your order id"
var userInfo = "user information created from previous step"

proximitySDK  
  .startLocationUpdates(orderID, userInfo, object : ProximitySDK.CallBacks{  
     override fun success(success: String) {  
         //your applicaiton will get store details from the success.
     }  
  
     override fun error(error: String) {  
         //this override method will show you the error occured in api or in sdk.  
     }  
  
})
</code></pre>
<p>This method creates foreground service along with the notification for accessing background location even after the application kills.</p>
<h2 id="find-background-location-is-running-or-not">Find background location is running or not</h2>
<pre><code>var isBackgroundServiceRunning = proximitySDK.isLocationServiceRunning()
</code></pre>
<p>This method will <strong>returns true</strong> if the background service is running.</p>
<h2 id="force-stop-location-updates-from-background">Force stop location updates from background</h2>
<pre><code>proximitySDK.stopLocationUpdates()
</code></pre>
<p>This method will forcefully stop location updates from background, pinned notification is also removed from the notification bar.</p>
<h2 id="register-for-localbroadcast-for-location-and-other-updates-from-sdk">Register for LocalBroadcast for location and other updates from SDK</h2>
<pre><code>LocalBroadcastManager.getInstance(this).registerReceiver(  
    mMessageReceiver, IntentFilter("GPSLocationUpdates")  
)

private val mMessageReceiver: BroadcastReceiver = object : BroadcastReceiver() {  
    override fun onReceive(context: Context?, intent: Intent) {  
  
        val location = intent.getStringExtra("location")  
        val userActivity = intent.getIntExtra("userActivity", 0)  
        val speed = intent.getDoubleExtra("speed", 0.0)  
        val batteryLevel = intent.getIntExtra("batteryLevel", 0)  
        val altitude = intent.getIntExtra("altitude", 0)  
        val isUserReached = intent.getBooleanExtra("userReached", false)  
        val isBatteryCharging = intent.getBooleanExtra("isBatteryCharging", false)  
  
        var activity = UserActivity(applicationContext).convertToString(userActivity)  
    }  
}
</code></pre>
<p>By registering with the <strong>Local Broadcast receiver</strong> your application will get the updates which is collected by the sdk.</p>
</div>
</body>

</html>
