# Elastos.NET.Carrier.Android.Demo
## Summary

The Demo introduces how to use the Carrier of Elastos’s Android SDK to achieve communication 
between phones and realizes a simple version of Chat Dapp.

## Loading library files
Download android.sdk-debug.tar.gz from the following website and unzip it, 
add the extracted files to the app lib path:
```
https://github.com/elastos/Elastos.NET.Carrier.Android.SDK
```

The loaded directory structure is as follows:
```
localhost:libs xuxinlai$ pwd
/Users/xuxinlai/blockchain/Elastos.NET.Carrier.Android.Demo/app/libs
localhost:libs xuxinlai$ tree
.
├── arm64-v8a
│   └── libcarrierjni.so
├── armeabi
│   └── libcarrierjni.so
├── armeabi-v7a
│   └── libcarrierjni.so
└── elacarrier.jar
```

## Configure and load NDK

If you have not yet installed NDK, refer to the Android official website to install NDK 
and add the following configuration in the build.gradle file:


```
ndk {
    moduleName "carrierjni"
    ldLibs "log"
    abiFilters "armeabi"
}

sourceSets {
    main {
        jniLibs.srcDirs = ['libs']
    }
}
```

## Initialize Carrier instance and start 
Before you initialize Carrier, you need to set the related parameters options and handlers, 
which are an instance of the following classes, respectively.

```
//1.Initialize the Carrier and get the related information
try {
   //1.1 Initialize the Instance
   carrierInst = Carrier.getInstance(options, handler);

   //1.2 get the Carrier address
   carrierAddr = carrierInst.getAddress();
   Log.i(TAG,"address: " + carrierAddr);

   //1.3 get the Carrier user id
   carrierUserID = carrierInst.getUserId();
   Log.i(TAG,"userID: " + carrierUserID);

   //1.4 start the net 
   carrierInst.start(1000);
   handler.synch.await();
   Log.i(TAG,"carrier client is ready now");

} catch (ElastosException e) {
    e.printStackTrace();
}
```

**Don't forget to add related permissions in file AndroidManifest.xml**


```
<uses-permission android:name="android.permission.INTERNET"/>
```

## Add friend

Add a friend by calling the function below:，
```
//2.1 add the friends
try {
    Log.i(TAG,"start add Frind");
    carrierInst.addFriend(friendAddr, "auto-accepted");
    Log.i(TAG,"end add Frind");
}catch (ElastosException e) {
    e.printStackTrace();
}
```

carrier.AcceptFriend(String friendId) function through overwrite the callback function 
onFriendConnection as below:
```
//2.2 verify the friends
public void onFriendRequest(Carrier carrier, String userId, UserInfo info, String hello) {
    try {

        if (hello.equals("auto-accepted")) {
            carrier.AcceptFriend(userId);
        }
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

## Send message to friend
send Message to friends by the function sendFriendMessage
```
//3.1 send the message 
try {
    Log.i(TAG,"start send message");
    carrierInst.sendFriendMessage("3uHutUcGb9Sc2VX87bwxuoejX7oDFRc2FuUkyYHxbNpG", "hello e");
    Log.i(TAG,"end send message");
}catch (ElastosException e) {
    e.printStackTrace();
}
```

receive the message by the onFriendMessage callback function as below:
```
//3.2 get the messsage from frined
public void onFriendMessage(Carrier carrier,String fromId, String message) {

    Log.i(CALLBACK,"address:" + fromId + "connection changed to: " + message);
}        
```
        