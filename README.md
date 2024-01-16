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
SO, cmake and the Java sdk are already installed.  We need the Android stuff.

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

### MORE TO FOLLOW

