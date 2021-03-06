# Shared Memory

The Shared Memory library allows for the creation of memory regions that may be simultaneously accessed by multiple Android processes or applications. Developed to overcome the Android 1MB IPC limitation, this Shared Memory library allows you to exchange larger amounts of data between your Android applications. 

<p align="center">
  <img src="https://github.com/NewtronLabs/SharedMemory/blob/master/Diagram.png" width="65%" height="65%" >
</p>

----

## How to Use 

### Setup

Include the below dependencies in your `build.gradle` project.

```gradle
buildscript {
    repositories {
        jcenter()
        maven { url "http://code.newtronlabs.com:8081/artifactory/libs-release-local" }
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.5.2'
        classpath 'com.newtronlabs.android:plugin:4.0.0'
    }
}

allprojects {
    repositories {
        jcenter()
        maven { url "http://code.newtronlabs.com:8081/artifactory/libs-release-local" }
    }
}

subprojects {
    apply plugin: 'com.newtronlabs.android'
}
```

In the `build.gradle` for your app.

```gradle
dependencies {
    compileOnly 'com.newtronlabs.sharedmemory:sharedmemory:4.0.0'
}
```

### Sharing Memory - Producer
From the application that wishes to shared its memory, allocate a shated memory region with a given name. 

```java
// Allocate 2MB
int sizeInBytes = 2*(1024*1024);
String regionName = "Test-Region";
ISharedMemory sharedMemory = SharedMemoryProducer.getInstance().allocate(regionName, sizeInBytes);
```

Write data to memory:
```java
byte[] strBytes = "Hello World!".getBytes();
sharedMemory.writeBytes(strBytes, 0, 0, strBytes.length);
```

Once an application has shared a memory region it can be accessed by other processes or application which are aware of it.

### Accessing Shared Memory - Consumer
In order for an application to access a region of memory shaered by an external application perform the following:

```java
// This is the application id of the application or process which shared the region.
String producerAppId = "com.newtronlabs.smproducerdemo";

// Name under wich the remote region was created.
String regionName = "Test-Region"

// Note: The remote application must have allocated a memory region with the same
//       name or this call will fail and return null.
IRemoteSharedMemory remoteMemory 
         = RemoteMemoryAdapter.getDefaultAdapter().getSharedMemory(context, producerAppId, regionName);

// Allocate memory to read shared content.
byte[] dataBytes = new byte[remoteMemory.getSize()];
String dataStr = new String(dataBytes);
Log.d("Newtron", "Memory Read:"+dataStr);
```

### Additional Samples
A set of more complex exmaples can be found in this repo's samples folders: **SmProducer** and **SmConsumer**. 

---
## License

https://gist.github.com/NewtronLabs/216f45db2339e0bc638e7c14a6af9cc8

## Contact

solutions@newtronlabs.com
