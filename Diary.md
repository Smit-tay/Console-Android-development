# Console-Android-development
## Adventures in developing C++ for Android from the console.

This project should serve as both a reference for newbies, and a diary of my experiences in developing a simple "Hello World" application for Android.  Along the way, I will attempt to keep this readme updated with useful information as I discover and work through problems.

It turns out to be surprisingly challenging to find good information on developing C++ applications for Android using commandline tools.  You might ask, why do I wish to make life hard for myself ?  Suffice it to say that my experience with IDE style development is increasingly unsatisfactory.  Tools like Eclipse, VSCode, and QTCreator have become so bloated that trying to accomplish trivial tasks is little short of exasperating.    I know I am not alone feeling like this; a quick search on Stackoverflow or other similar sites will reveal countless similar opinions.

My goal is to create a simple, easy to understand, minimalist set of instructions.

### Starting assumptions
1. A Linux operating system
2. sudoer permission

### Basic environment
It seems that at a minimum we will require:
1. CMake - minimum version unknown
2. Java SDK - minimum version unknown
3. Android SDK - version unknown
4. Android NDK - version unknown
5. Python - version unknown
6. C++ compiler - unknown version

Python isn't explicitely mentioned in most of the references I consulted, but, I'm pretty surer it's needed.

Note, I am a relatively experienced software developer, so, I already have some tools installed on my machine.

```
localhost:~> cmake --version
cmake version 3.28.1

CMake suite maintained and supported by Kitware (kitware.com/cmake).

localhost:~> javac --version
javac 11.0.21
localhost:~> python3 --version
Python 3.11.7
localhost:~> g++ --version
g++ (SUSE Linux) 13.2.1 20231130 [revision 741743c028dc00f27b9c8b1d5211c1f602f2fddd]
Copyright (C) 2023 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.


```
SO, CMake, Java sdk, Python3 and a C++ compiler are already installed.  We need the Android stuff.

The SDK is available without downloading the Developer Studio, but is hidden at the bottom of Developer Studio download page.
It should be named something like "commandlinetools-linux-XXXXXXXXXX_latest.zip"

Check here: [developer.android.com SDK](https://developer.android.com/studio#downloads)

The NDK - which I guess is an abbreviation for **N**ative **D**evelopment **K**it is also available.

Check here: [developer.android.com NDK](https://developer.android.com/ndk/downloads)

I downloaded the latest "stable" or "Long Term" versions of both of those - I guess.
Then I unzipped them into my home directory.

The SDK tools isn't actually an SDK in the usual sense.  In order to install the actual SDK, you are essentially forced to use a java application named "sdkmanager" which ships with the SDK commandlinetools archive.

Don't forget, Android is Java at heart, and Android developers are really just over-enthusiastic Java developers.
**EVERYTHING** in java is more complicated than it needs to be, just accept it.

### Our first problem !
Following the instructions for the commandlintools [sdkmanager](https://developer.android.com/tools/sdkmanager), we immediately encounter a problem

```
localhost:~/cmdline-tools/latest> bin/sdkmanager --list
Error: LinkageError occurred while loading main class com.android.sdklib.tool.sdkmanager.SdkManagerCli
	java.lang.UnsupportedClassVersionError: com/android/sdklib/tool/sdkmanager/SdkManagerCli has been compiled by a more recent version of the Java Runtime (class file version 61.0), this version of the Java Runtime only recognizes class file versions up to 55.0
```

So much for Java's "write once, run anywhere" promise.

It appears that class file version 61 is provided by Java 17, and 55 is Java 11.  This means, I have to upgrade my Java version. I very likely will require the java 17 development environment as well as the runtime, so, I do this:
```
localhost:~/cmdline-tools/latest> sudo zypper install java-17-openjdk java-17-openjdk-devel
```

And as if by magic, the sdkmanager responds to "sdkmanager --list" with hundreds of lines of available packages. Now, how do I determine which I require ?
Looking through the output, I notice many lines looking something like what I expect to see
```
platforms;android-10                                                                     | 2             | Android SDK Platform 10
```
The instructions for the sdkmanager aren't particularly enlightening in this regard, but, I am fairly confident that "Android SDK Platform XX" is what I am looking for.
It's not at all clear if I should install the latest version - internet search !

It appears that Android developers haven't learned anythign from the chaos that is Java versioning, and have instead opted to reproduce - even extend - that chaos.  How anyone can justify [THIS KIND OF IDIOCY](https://developer.android.com/tools/releases/platforms) when it comes to versioning will remain a mystery.  Sure, the OS version is seperate from the development environment used to create it - or at least it can be, for example with Linux, I have a glibc version that bears no semblance to the OS version.  BUT, that's because they are independent of each other.  In the case of Android - well, that's simply not the way it works.  Every new Android OS release involves a new version of the SDK, in fact, they're basically the same thing.  So, why wouldn't you label the SDK with the OS Version.  Because - that's not how Java crazies think.

At the time of writing, the "current" (assuming you have an Android device less than 3 years old) Android version is 14, which corresponds to SDK version 34.
Looking through the list of packages, I see this:
```
platforms;android-34            | 2             | Android SDK Platform 34                                             
platforms;android-34-ext10      | 1             | Android SDK Platform 34-ext10                                       
platforms;android-34-ext8       | 1             | Android SDK Platform 34-ext8  
```
Again, back to google to figure out what this means.

Oh My God !  
I was right - they really are extending the chaos - [Look at this](https://developer.android.com/guide/sdk-extensions)
>Starting with Android 11 (API level 30), Android devices include a set of SDK Extensions. When new APIs are added, they are included in an API level, but may also be included in an SDK extension of a particular version. For example, the ACTION_PICK_IMAGES API for Photo Picker was added to the public SDK in Android 13 (API level 33), but is also available through SDK extensions starting in R Extensions Version 2. SDK Extension names correspond to an integer constant–either a constant from Build.VERSION_CODES, or one defined in the SdkExtensions class (such as SdkExtensions.AD_SERVICES).

Clear as mud !

Reading the entire "SDK Extensions" page (linked above) several times hasn't helped me undertsand the answer to this question.  So, for now, I assume I don't need to care about it.

So, I go ahead with this command:
```
bin/sdkmanager "build-tools;34.0.0" "platforms;android-34" "cmdline-tools;latest" "platform-tools"
```

And, the SDK Manager downloaded stuff **into my home directory !**.  Why there ?  Why not underneath the current directory ?  Why, why why 



