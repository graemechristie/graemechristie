---
layout: post
title: "ASP.NET vNext on OSX and Linux"
date: 2014-05-26 21:32
comments: true
categories: ASPNet vNext OSX Linux
---

### So what is ASP.NET vNext ?

Microsoft have recently released a preview of the next iteration of their ASP.NET platform. I'm not going to go into the details here, people like [Scott Hanselman](http://www.hanselman.com/blog/IntroducingASPNETVNext.aspx) have already done a fantastic job of that. I will however say that the changes, both technical and cultural, are huge. Two of those changes are particularly relevant to this blog post.

Firstly, ASP.NET vNext is fully open source. People like you and me are able to get in on the ground floor, try out the bits and pieces and even contribute -  pretty much from the project's inception.

Secondly, ASP.NET vNext is cross platform, and embraces non Windows hosts as first class citizens. Microsoft are fully integrating Mono and Linux into their build environment and test matrix, and are actively working with the community to make Mono a top class platform for hosting ASP.NET. That being said, these are early days, and the team is still ironing out issues with the Mono environment.

### Getting ASP.NET vNext up and running on OSX and Linux

There are two main steps to getting to the point where we can run ASP.NET vNext applications on our non Windows system.

The first is to install Mono. Normally this would be a no brainer; however as all this stuff is very shiny and new there are fixes and features that are currently only in the bleeding edge source code of Mono and have not been released as a package as yet. In order to get these fixes we are going to need to build mono from the latest source code located in the mono git repository.

The second step is to install the "K Runtime Environment" or KRE. This is the command line environment that will build and run (not that there is really a distinction anymore) projects from their new project.json project files.

Installation of the KRE is handled by the "K Version Manager" (KVM). This is a simple app that can install multiple versions of the KRE side by side, and allow you to easily switch between them.

### Building Mono

I don't want to go into too much detail here. There are plenty of [guides around the internet](http://www.mono-project.com/Compiling_Mono) on installing mono from source.

* Install Mono as per the instructions at [The Mono Github page](https://github.com/mono/mono).
* You will need an earlier mono release installed in order to build Mono from source (Mono uses Mono to build itself)
* You will need autoconf, libtool and and a few oher common dev tools installed on your system.
* If you are on linux, you will need to run `mozroots --import --sync` after installing mono to avoid certificate/trust issues down the line
* If you are on OSX you will want to edit /private/etc/paths and move the line `/usr/local/bin` before the line `/usr/bin` so the system finds the new mono version on the path before the old one.

 _caveat: This may totally break your system if you have lots of crazy stuff in /usr/local/bin. If your not sure about this, wait for a supported official release of Mono_

Once all this is done, run `mono --version`. You should see that you are running Mono version 3.4.1 or newer.

![mono --version](https://s3-ap-southeast-1.amazonaws.com/uploads-ap.hipchat.com/36968/482094/fPMtOGUiN1vyLzx/upload.png)  

If you do not, check your `$PATH` variable and go back over the mono install steps.

### Installing KVM and the K Runtime Environment

Installing KVM is super simple. If you like you can go to the [Readme for the AspNet Home project](https://github.com/graemechristie/Home/tree/KvmShellImplementation#osxlinux) and follow the "Getting Started" instructions there, however that involves cloning the Home Repository which is only required for that quick start demo.

KVM can be installed on any Linux or OSX system (that has bash or zsh and curl installed) with a single line.

```
curl https://raw.githubusercontent.com/graemechristie/Home/KvmShellImplementation/kvmsetup.sh | sh && source ~/.kre/kvm/kvm.sh && kvm upgrade
```

This will:

* download kvm.sh and save it in ~/.kre/kvm/kvm.sh
* add the command to run kvm.sh on every login to bash/zsh.
* kvm.sh will then be run via the `source` command. This adds the `kvm` command to the current shell.
* run `kvm upgrade`. This will download the latest KRE package, extract it to .kre/packages and add the bin folder to your path.

Once this is done, all of the KRE commands will be available from shell prompt. Primarily this will be the `k` command used to run ASP.NET vNext Projects, and the `kpm` command used to restore packages.

Additionally the kvm command can be used to install other version of KRE side by side for the user, switch between versions, list the installed versions and set up aliases. See the [ASP.NET Home Project Readme](https://github.com/graemechristie/Home/tree/KvmShellImplementation#running-the-samples) for examples of the various `kvm` commands.

### Okay, so I've got a Shiny New KRE ....

So let's point it at something. We could download one of the many samples from the aspnet repository, however just to prove we have everything we need to build and run ASP.NET vNext apps on OSX and Linux, lets create a very basic project from scratch.

* Create a folder called _HelloKRuntime_ somewhere on your system and `cd` into that folder

* Create a file called `project.json` and copy the following text into it. This is your ASP.NET vNext project file .. and not an angle bracket to be seen !
```json
  {
    "dependencies": {
      "System.Console": "4.0.0.0"
    },
    "configurations": {
      "net45": {},
      "k10": {}
    }
  }
```

* Create a file called Program.cs and copy the following text into it.
```aspx-cs
  using System;

  public class Program
  {
      public static void Main()
      {
          Console.WriteLine("Hello K Runtime !");
      }
  }
```

* type `kpm restore -s https://www.myget.org/F/aspnetvnext/`
 The K Package Manager will head off to the nuget repository and fetch `System.Console` and all its dependencies, based on the entry in your project.json file. Note that there is no need to use Nuget to install this package. The project.json entry is all the info kpm needs to fetch your dependencies.

 _Note: Because the ASP.NET vNext project is currently using their own nuget feed, we need to supply the nuget source on the command line. This can be provided in a NuGet.Config file in your Solution folder, and once the vNext is released you shouldn't need one at all unless you are using your own private nuget feeds._

 ![kpm restore](https://s3-ap-southeast-1.amazonaws.com/uploads-ap.hipchat.com/36968/482094/VORbSwiBWtopBnQ/upload.png)

* Now type `k run` from the command line. You should see your system burst to life and utter those immortal words

 ![Hello K Runtime](https://s3-ap-southeast-1.amazonaws.com/uploads-ap.hipchat.com/36968/482094/tAex4VvNajwBtUS/upload.png)

* As an example of how the new Configuration system ties into the environment, try typing `export KRE_TRACE=1` and then `k run` again. You will see the compiler output as well as the "Hello K Runtime" message.

 ![Hello Compiler Output](https://s3-ap-southeast-1.amazonaws.com/uploads-ap.hipchat.com/36968/482094/iSZ9yKiAaxzlWdO/upload.png)


### In Summary

Hopefully this post has demonstrated that cross platform ASP.NET vNext functionality is available now. There are still rought edges, but progress is being made daily and first class support for OSX and Linux environments is on the Horizon. If you are having issues getting this working, feel free to drop into the [aspnetvnext room on Jabbr](https://jabbr.net/#/rooms/AspNetvNext) and someone should be able to help you out.

UPDATE: If you'd like to try something more involved than the trivial console application above, the simple [Hello World V Next](https://github.com/davidfowl/HelloWorldVNext) web example might be a good next step. 
