---
layout: post
title: "ASP.NET vNext on OSX and Linux Redux"
date: 2014-09-30 22:13:57 +0800
comments: true
categories: ASPNet vNext OSX Linux
---

Things have changed a fair bit since my last post back in May, and getting the latest and greatest ASP.NET vNext up and running with Mono is now reasonably straight foreward,
so I thought I'd do a quick update on installing ASP.NET vNext on OSX and Linux.

### Install Mono

Xamarin now maintains mono packages for OSX and most common Linux distributions, and are committed to a regular release schedule, so getting mono up and running is now a much simpler affair. Currently, there are supported packages for Mono. Installation is generally as simple as adding the Xamarin repository to your packages sources, and importing their GPG key) and then installing. I am going to cover OSX and Ubuntu 14.04 here, but easy instructions for most distros can be found http://www.mono-project.com/docs/getting-started/install/linux/.

##### OSX

* Simply download the Mono MDK from http://www.mono-project.com/download/#download-mac and run the installer.


##### Ubuntu 14.04

* Add the Xamarin GPG key to apt
```
wget http://download.mono-project.com/repo/xamarin.gpg && sudo apt-key add xamarin.gpg`
```
* Add the Xamarin repository to apt
```
sudo sh -c 'echo "deb http://download.mono-project.com/repo/debian wheezy main" > /etc/apt/sources.list.d/mono-xamarin.list'
```
* Update your package cache
```
sudo apt-get update
```
* Install the mono development package
```
sudo apt-get install mono-devel
```

* We will need to install some certificates on linux, to avoid an getting exception when downloading packages with nuget package restore. This is not required on OSX.
```
 sudo certmgr -ssl -m https://go.microsoft.com
 sudo certmgr -ssl -m https://nugetgallery.blob.core.windows.net
 sudo certmgr -ssl -m https://nuget.org
 mozroots --import --sync
```


##### Check Your Mono Installation

* Open a new shell and type `mono --version`. You should be running mono version 3.8.0 or newer.

    ![Mono Version](https://s3-ap-southeast-1.amazonaws.com/uploads-ap.hipchat.com/24631/1103057/CwcEnLnN2p3lZnh/upload.png)


If not, you probably still have an old version of Mono on your path. This might be the case if you had previously installed Mono from source (as per my last blog post). You will want to make sure you remove your old installation. Running `make clean` from the mono source code folder is probably the best way to do this. Additionally, remove any other installation that may have been installed via a third party package manager such as homebrew on OSX.

### Install KVM and the K Runtime

* If your on Linux, you may need to install curl.
```
sudo apt-get install curl
```
_If you have any old (alpha) versions of kre installed, I recommend removing them from your system and starting fresh, and then starting a new shell. This will avoid any package sources being carried over in environment variables. I'm not going to paste the commands here, for fear of people nuking thier LOLcats image collection accidentally and blaming me .. but effectively you just want to remove the `~/.kre` folder from your home directory, and start a new shell._

* Bootstrap your KVM installation by downloading and running the kvm install script from the [aspnet/Home](https://github.com/aspnet/Home) respository. We are using the `dev` branch here, which will download a kvm that is pointing at the feed for the latest beta packages. At the time of writing the master branch is still pointing at the alpha CTP.
```
curl https://raw.githubusercontent.com/aspnet/Home/dev/kvminstall.sh | sh && source ~/.kre/kvm/kvm.sh
```
* Install the latest K Runtime
```
kvm upgrade
```
* Verify the latest install
```
kvm list
k --version
```
![KVM installed](https://s3-ap-southeast-1.amazonaws.com/uploads-ap.hipchat.com/24631/1103057/Me72unTWXm3RF85/upload.png)

You should now have one of the latest betas installed. The active version from `kvm list` should reflect the actual version of kre that you see when you run `k --version`.

 
### Try out the Hello World vNext Demo from David Fowlers github repository

David fowler has created a Demo app for OSX and linux, and put it up in his [personal repository on GitHub](https://github.com/davidfowl/HelloWorldVNext). To check everything is working properly, we are going to clone it, and run it locally on our OSX or Linux installation.

* First, install "git" if you don't already have it. OSX users can get it from [git-scm.com](http://git-scm.com/download/mac), or on Ubuntu
```
sudo apt-get-install git
```
* Clone the HelloWorldVNext repository
```
git clone https://github.com/davidfowl/HelloWorldVNext.git
```
* Navigate into the HelloWorldVNext directory
```
git clone https://github.com/davidfowl/HelloWorldVNext.git
```
* Restore Packages
```
kpm restore
```
* Navigate to the src/helloworldweb directory
```
cd src/helloworldweb
```
* Run the web application
```
k web
```
* Open a browser and go to http://localhost:5000. If everything is in order, you will see "Hello World" staring back at you ....

  ![Great Success](https://jabbrlive.blob.core.windows.net/jabbr-uploads/clipboard_cf58.png)

### Final Notes

The steps above are the bare minumum to get you up and running with vNext on Linux or OSX. The project is still (only barely) in beta, and is not ready the prime time yet. That being said, the ASP.NET vNext team at Microsoft have shown a huge commitment to cross platform support for the next iteration of ASP.NET. ASP.NET, running in production, on Linux, fully supported by Microsoft, __will be a thing__, and probably within the next twelve months. The time to get in there, get your hands dirty and kick the tires is now .. while the devs are ready and able to respond to your feedback.

If you have any problems or feedback, or just want to hang around and see what's going on, jump into the ASP.NET vNext chat channel at https://jabbr.net/#/rooms/AspNetvNext, there is almost always someone around.






 




















