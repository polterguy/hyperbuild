
# Hyperbuild, Phosphorus Five's build system

Hyperbuild is the build system for [Phosphorus Five](https://github.com/polterguy/phosphorusfive), which allows you to 
reproduce your Phosphorus Five installation, any ways you see fit, and create a binary release of the system. 
This is done by taking the existing system's state, whatever that is, minus any data, users, passwords, settings, etc -
And create a binary release of the system, allowing you to cryptographically sign addon modules, choose
which modules are to be in the main distribution, etc. Hyperbuild has a lot of settings and options, depending upon whether or
not you choose to make the _"Bazar"_ a part of your main distribution - But for the most parts, 95% of these are irrelevant, 
unless you want to host your own Bazar.

![alt screenshot](media/screenshot-4.png)

If you have created your own modules, or changed existing modules, Hyperbuild will allow you to create
a binary release of your system, according to whatever changes you have applied to your original system.

**Notice** - When you run Hyperbuild, it will copy your DLLs from your _"bin"_ folder, as they are in its current version.
This implies that you'd probably want to make sure you are running the system in its _"Release"_ build, if you are
creating your own build, from a source version of Phosphorus Five.

## Usage

Hyperbuild has a lot of settings, which allows you to build more or less P5 exactly as you see fit. Below you can find
the instructions on how to use it.

### Main distribution

This is your most important settings, and declares how you want your main distribution to be like. For most people, understanding
these parts is enough - Unless you want to host your own Bazar.

First you must choose which modules you want to include in your main distribution zip file. By default, only _"micro"_ is selected. 
_"micro"_ is normally crucial, since most P5 modules depends upon [Micro](https://github.com/polterguy/micro) being installed to 
function properly. If you don't include the Bazar in your main configuration, you will have a _"static"_ system, without any Bazar 
integration - Which is probably quite useful, if all you want to do is to create and distribute your own apps, and/or any of the 
integrated apps, as a stand alone web application/operating system.

Whichever other modules you include, will be included by default, in your own distribution, in your main .zip file, 
for your own Phosphorus Five distribution. The system works by traversing all folders inside of your _"p5.webapp/modules/"_
folder, implying that any custom modules you have created yourself, will be possible to include in your main distribution here.

It is definitely much easier to build a _"static"_ system, than it is to create a fully fledged Bazar system, with your own
Bazar implementation. For most users, this would probably suffice, and you can simply ignore everything below this sub-header.

**WARNING** - By default, the build process will include your _"web.config"_ file. If you have passwords, or other types of sensitive
data in this file, you should probably **not include your web.config** file in your main release build. How you want to distribute
an actual working web.config file if you ommit this file, is up to you to solve yourself.

However, the default web.config file, should not in general contain any sensitive data, since it relies upon MySQL installed, with
an empty root password, and no network drivers installed. In general, including the web.config file shouldn't be a problem, unless 
you've got some customisations of your system, which is outside of the scope of Hyperbuild.

When you have created your _"binaries.zip"_ release file, you can use the [following script](https://github.com/polterguy/phosphorusfive/blob/master/install.sh) as 
a template for installing your zip file on Linux/Ubuntu servers.

### Additional modules

If you include the _"bazar"_ into your main build, you are given the option to build additional modules yourself.
This is normally only relevant if you wish to create your own Bazar implementation - At which point, you'd normally have to 
cryptographically sign your additional modules, with your own private PGP key. This is a security feature in the Bazar, eliminating having
your users downloading and installing malicious code - Due to the only code that is allowed to be installed through the Bazar, are
programs that have been explicitly cryptographically signed by a trusted private PGP key.

The system will create one additional .zip file for each of these additional modules you choose to build. If you choose to cryptographically
sign these modules, which is done by choosing a private PGP key, **and supplying a password**, to have your private PGP key released from your 
GnuPG installation - These additional zip files will be cryptographically signed with your own private PGP key, and be 
named _"some-module.zip.pgp"_, allowing you to download these files after the build process has finished, and distribute them through your 
own Bazar.

**Notice** - If you choose to make the _"Bazar"_ a part of your main distribution, but you do not cryptographically sign your own modules, the
default P5 Bazar will be used in your main distribution. This is the main P5 Bazar, where all the apps are signed with my PGP key.

### How the Bazar work

The Bazar is Phosphorus Five's _"AppStore"_, and allows you to securely distribute your own additional modules, either for a fee, or gratis -
And such allow consumers of your _"web operating system"_, to install additional modules, as they see fit.

The Bazar expects all of these additional modules to be cryptographically signed with your own private PGP key. This prevents an
adversary to inject malicious code into your user's systems, since by default the system will only trust modules, that have been cryptographically
signed, with a private PGP key, that must exist in the _"/modules/bazar/configuration/trusted-app-distributors.hl"_ file.

When you run Hyperbuild, it will create a new _"trusted-app-distributors.hl"_ file for you automatically, if you supply a private PGP key, and its
password to cryptographically sign any additional modules. This allows anything that has been cryptographically signed with the specified PGP key,
to be automatically installed, as an additional module, into your user's systems.

#### The Bazar(s) declaration file

This file defines where your Bazar(s) exists on the internet, and contains references to a Hyperlambda file on the internet, that the system
will automatically check (periodically), to see if there have been added new additional modules, that the user can download and install, on his
own system.

As you create your build, you will be asked to provide such a Bazar declaration file, which will be injected dynamically into your 
own _"/modules/bazar/configuration/bazars.hl"_ file, and such becomes a link to your actual Bazar implementations. Basically, this is a link, 
to one or more Hyperlambda file(s), on the internet, through which you want to inform users of your system, about new modules, and/or upgrades 
to existing modules.

Normally, this file should simply look like the following.

```
bazar:"https://my-server.com/my-bazar.hl"
```

If the above URL is pointing to your Bazar content file, your Hyperbuild settings should resemble something like the following.

![alt screenshot](media/screenshot-3.png)

As you update your _"Bazar content files"_, your users will automatically and dynamically, have the option to install new apps into their systems. The default Bazar
declaration file, contains additional HTTP headers, necessary to retrieve the default Phosphorus Five Bazar. You can add any amount of additional
static HTTP headers, as children to your **[bazar]** arguments, inside of your Bazar declaration file. However, by default, an *"If-Modified-Since"* 
header will be intelligently added, to avoid downloading Bazar content files, that have no new content. This is a feature to avoid downloading huge 
Bazar content files, that have not been changed, and hence will significantly speed up the traversal of your Bazar(s)' content files.

#### The Bazar content file(s)

This file contains all your additional modules, that the user can install, either for a fee or gratis into his system. It has a structure, where
each app is declared as an **[app]** node, in addition to a friendly _"name"_ at the top.

This is the file that needs to be updated every time you add a new module, and or update an existing module, to allow your users to update their 
systems. When you have created your build, an example Bazar content file will be shown to you, which you can modify as you see fit. This file must
have a special structure, where each **[app]** declaration must resemble the following.

```

/*
 * Friendly displayed name of your repository.
 */
name:My cool repository


/*
 * Some App
 */
app:some-app-unique-id

  // Name of your app.
  name:Some App

  // Short description of your app.
  description:Some app does something really cool, and here is a short description of it.

  // Conditional Active Event, which if found, assumes the app is already installed on user's system.
  condition:some-app.condition-to-check-if-app-is-installed

  // Link to PGP signed zip file for downloading and installing app.
  url:"https://my-server.com/modules/some-app.zip.pgp"

  // A more detailed description of your app.
  // Notice, use Markdown here if you wish.
  author-readme:@"Longer description goes here!! Notice, use **Markdown** if you wish!"

  // Price of your app, and/or license.
  price:Free/GPL

  // The name for your folder.
  // This will be the folder name inside of "/modules/" in your users' systems.
  // USE SOMETHING UNIQUE HERE!!
  local-url:sephia-five

  // And image that will be shown for your app, before installation.
  image:"https://phosphorusfive.files.wordpress.com/2017/08/vault-icon-2.png"

  // The PGP key's fingerprint, for the PGP key that was used to sign this particular app.
  developer-key:5E11AEF421CDB0A7A9A5C6B4AED9D04F43BE2AAD
```

**Notice** - Although the Bazar itself supports having multiple _"trusted app distributors"_, Hyperbuild does not support creating
a trusted distributor file with more than one PGP key within. If you wish to create a distribution with multiple trusted
distributors, feel free to modify the _"trusted-app-distributors.hl"_ file by hand yourself.

You should host your Bazar content file, at a URL, being e.g. `https://my-server.com/my-bazar.hl`. Whenever your Bazar content file 
later becomes updated, your users will have the option of downloading and installing new apps, and/or upgrades to your existing apps.
This allows you to modify existing systems, previously installed by your users, automatically - Allowing anyone to add additional new apps
to their existing systems.

