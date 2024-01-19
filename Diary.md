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
platforms;android-10             | 2             | Android SDK Platform 10
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
ANyway, I now have the following directories (within my $HOME)
```
drwxr-xr-x 1 me me  360 Jan 16 09:47 android-ndk-r26b
drwxr-xr-x 1 me me   20 Jan 17 14:47 platforms
drwxr-xr-x 1 me me  288 Jan 17 14:47 licenses
drwxr-xr-x 1 me me   12 Jan 17 14:47 build-tools
drwxr-xr-x 1 me me  246 Jan 17 14:47 platform-tools
drwxr-xr-x 1 me me   28 Jan 17 14:47 cmdline-tools
```

The NDK is from my manual download.  The cmdline-tools is also from my manual download, but, has been updated.
The platform directory appear to contain the actual SDK (if you can call it that), since it contains a subdirectory named "android-34"

So, I am halfway confident I am ready to start putting together an application.

To start with we want a somewhat conformant directory structure.  Because this is essentially a Java app, we have to have an unnecessarily complicated structure, or the compiler will complain (unless of course we provide endless build options and ENV variables before hand).

I am working from within a subdirectory of my main development directory ( '/home/me/dev' - where all my projects live).  That subdirectory is named Hello-Android, since it's simple, classical and timeless.  Within that subdirectory, I will want to make the following:

```
# First create semi-obligatory directories
mkdir -p res/layout java/hello build/gen

# Now, create some obligatory files 
touch AndroidManifest.xml ./res/layout/activity_main.xml ./java/hello/MainActivity.java
```

Now we can edit the three files we created

The [AndroidManifest.xml](https://developer.android.com/guide/topics/manifest/manifest-intro) file is a sensible idea, and mandatory. Luckily, for now, we don't need to know much about it, other than the absolute basics.  Ours will look like this:

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="hello"
          versionCode="1"
          versionName="0.1">
    <uses-sdk android:minSdkVersion="34"/>
    <application android:label="Hello">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
    </application>
</manifest>
```

Next is the [layout](https://developer.android.com/develop/ui/views/layout/declaring-layout) file, also obligatory for reasons that aren't clear - ours is named res/layout/activity_main.xml and obeys the usual weird Java naming convention with underscores

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/my_text"/>
</LinearLayout>
```

And, finally, the java code for our trivial application in java/hello/MainActivity.java

```
package hello;

import android.app.Activity;
import android.os.Bundle;
import android.widget.TextView;

public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        TextView text = (TextView)findViewById(R.id.my_text);
        text.setText("Hello, world!");
    }
}
```

# Now the problems begin
Like many people, the Android developers are only human !  So, they makes a lot of mistakes, then try to cover them up by making more mistakes.  This appears to be exactly what happened during the development of Android.  At some stage the developers realised that the build process for producing a "package" (which, let's face it, is a Java jar file) was a nuiscance.  

Here I lay out the build steps, before continuing to explain why this is a nuiscance, and what to do about it - and then, what the Androidn developers did about it.

## Building your app from the commandline
The steps are as follows:
### 1. AAPT2 (Android Asset Packaging Tool)
aapt2 is the replaceemnt for the original aapt tool from the early days of Androidn development. The first thing you need to do is compile your Manifest, along with your 'resources' into a binary form that can be used later in the build process.  You can read more about [aapt2](https://developer.android.com/tools/aapt2).
### 2. javac - The Java compiler
Like I said, Android apps are basically Java apps, so, you need to compile your Android Java code into byte code.  This produces 'class' files, one per Java source file.
### 3. d8 - DEX Dalvik bytecode format compiler
Now that your Java files are in java bytecode format, they need to be converted to Dalvik bytecode format (actually, now it's Android Runtime (ART), the successor of Dalvik).
So, now we see the absolute craziness.  Android developers weren't satisfied with Java, so they made it more complicated.  The d8 compiler is a replacement for the dx compiler (the original DEX compiler for Android), which is a replacement for the javac compiler, which, let's face it, is a replacement for a C++ compiler.  The d8 compiler also pulls in the pre-compiled resources generated by the aapt2 tool.  BUT, let's be clear here, these tools aren't actually replacements, they're dependents.  The d8 compiler doesn't compile Java code, it compiles java byte code and whatever the output of aapt2 is.  BTW - the real reason for Dalvik was that Google didn't want to pay Java royalties.   So they pretended to create a new "clean room" version of Java, which oddly enough still requires the javac compiler to work.
### 4. aapt2 (again)
At thsi stage, you need to re-package the stuff that you already packaged (resources), plus the new stuff that you packaged (Dalvik byte code) after originally packaging it (Java byte code)
### 5. zipalign - because aapt2 isn't very good
zipalign appears to be used to make up for a shortcoming in aapt2 (which wasn't fixed when they upgraded from aapt).  Namely, that the "package" or archive generated by aapt2 isn't very memory efficient.  Don't believe me, read it for yourself:
> "Use zipalign to optimize your APK file before distributing it to end users" [read it here](https://developer.android.com/tools/zipalign)
### 6. apksigner - adding a useless certificate to your package
Now, at this point you might be thinking, "wait, isn't signing a good idea?".  The answer to that *would* be yes, if the key was actually verified.  But, Androids own documents contain this gem:
> "Android currently does not perform CA verification for application certificates."

## This looks kind of familiar.
A serious student of software development would be excused for observing that this seems like the pre-process, compile, link stages of C/C++, except more confusing, and from all appearances unnecessarily complicated.  That student would be absolutely correct !

### How to build your app - continued
Now, back to the discussion of how to build.  You see, those steps, and some optional ones we haven't discussed, are tedious to run from the command line, so naturally, it's better to build a script for such things.  The problem with scripts is that they generally aren't compatible across platforms.  Enter, cross platform build systems like Make, CMake, Meson, and others.

Normally re-inventing the wheel is discouraged - unless your wheel is really something special.  If you invent a wheel without a bearing, please use it in preference to one that does have one.

Or, in the case of Android development, choose the newest, most unrefined build system available at the time, and go for it.  So, that's what they did.  [Gradle ](https://docs.gradle.org/current/userguide/userguide.html) was that choice.  It was a relatively unknown, immature product at the time, so why not use it ?!?!?!?   Zero points for guessing what language Gradle was initially developed as a build system.  Yes, rather than improve Ant, or Maven, or use existing tools like Make, etc. the Android devs chose this java based, java centric build tool to automate the craziness of the Android development cycle.

And, it stuck.

My linux distro doesn't provide gradle as a standard package.  In fact, from what I can tell, not may do.  It looks liek the usual way to get Gradle is to download an archive from the Gradle website, unzip it into the location of your choice, set a few env. variables, then hope it works.

Let's do that.
