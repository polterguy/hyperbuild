
# Hyperbuild, Phosphorus Five's build system

This is the build system for [Phosphorus Five](https://github.com/polterguy/phosphorusfive), which allows you to 
reproduce your Phosphorus Five installation, any ways you see fit, and create a binary release of the system. 
This is done by taking the existing system's state, whatever that is, minus any data, users, passwords, settings, etc -
And create a binary release of the system, allowing you to cryptographically sign modules, choose
which modules are to be in the main distribution, etc.

![alt screenshot](media/screenshot-2.png)

If you have created your own modules, or changed existing modules, Hyperbuild will allow you to create
a binary release of your system, according to whatever changes you have applied to your original system.

## Usage

Hyperbuild has a lot of settings, which allows you to build more or less P5 exactly as you see fit. Below you can find
the instructions on how to use it.

### Main distribution

This is your most important settings, and declares how you want your main installation to be like.

First you must choose which modules you want to include in your main distribution zip file. By default, the modules
selected for you are only the _"bazar"_ and _"micro"_. _"micro"_ is normally crucial, since most P5 modules depends
upon Micro being installed to function correctly. However, if you don't want to feature your own Bazar, you can
remove the _"bazar"_ module from your main distribution. If you don't include the Bazar in your main configuration,
you will have a _"static"_ system, without any Bazar integration - Which is probably quite useful, if all you want to
do is to create and distribute your own apps, and/or any of the integrated apps, as a stand alone web application.

Whichever other modules you include, will be included by default, in your own distribution, in your main .zip file, 
for your own Phosphorus Five distribution. The system works by traversing all folders inside of your _"p5.webapp/modules/"_
folder, implying that any custom modules you have created yourself, will be possible to include in your main distribution here.

It is definitely much easier to build a _"static"_ system, than it is to create a fully fledged Bazar system, with your own
Bazar implementation. For most users, this would probably suffice, and you can simply ignore everything below here.
