---
layout: post
title: "ASP.NET vNext on OSX and Linux Redux"
date: 2014-09-30 22:13:57 +0800
comments: true
categories: 
---

Things have changed a fair bit since my last post back in may,so I thought I'd do a quick update on the state of ASP.NET vNext on OSX and Linux, and how to get up and running now things are a bit more mature.


### Install Mono

Xamarin now maintain mono packages for OSX and most common Linux distributions, and are committed to a regular release schedule, so getting mono up and running is now a much simpler affair. Currently, there are supported packages for Mono. Intallation is generally as simple as adding there repository to your packages sources (and typically importing thier GPG key) and then installing. I am going to cover OSX and Ubuntu 14.04 here, but easy instruction more most distros can be found here at http://www.mono-project.com/docs/getting-started/install/linux/.

##### OSX

* Simply download the Mono MDK from http://www.mono-project.com/download/#download-mac and run the installer.


##### Ubuntu 14.04

* Add the Xamarin GPG key to apt
```
wget http://download.mono-project.com/repo/xamarin.gpg && sudo apt-key add xamarin.gpg`
```
* Add the Xamarin repository to apt
```
echo "deb http://download.mono-project.com/repo/debian wheezy main" > /etc/apt/sources.list.d/mono-xamarin.list
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


If not, you probably still have an old version of Mono on your path. This might be the case if you had previously installed mono from source (as per my previous blog post). You will want to make sure you remove your old installation. Running `make clean` from the mono source code folder is probably the best way to do this. Additionally, remove any other installation that may have been installed via a third party package manager such as homebrew on OSX.

### Install KVM and the K Runtime

* If your on Linux, you may need to install curl.
```
sudo apt-get install curl
```
* If you have any old (alpha) versions of kre installed, I recommend removing them from your system and starting fresh, and then starting a new shell. This will avoid any package sources being carried over in environment variables. I'm not going to paste the commands here, for fear of people nuking thier home directories accidentally and blaming me .. but effectively you just want to remove the `~/.kre` folder from you home directory, and start a new shell.


* Bootstrap your KVM installation by downloading and running the kvm install script from the asp.net/Home respository.
```
curl https://raw.githubusercontent.com/aspnet/Home/master/kvminstall.sh | sh && source ~/.kre/kvm/kvm.sh
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

David fowler has created a Demo app and put it up in his [personal repository on GitHub](https://github.com/davidfowl/HelloWorldVNext). To check everything is working properly, we are going to clone it, and run it locally on our OSX or Linux installation.

* First, install "git" if you don't already have it. OSX users can get it from [git-scm.com](http://git-scm.com/download/mac), or on Ubuntu
```
sudo apt-get-install git
```
* Clone the Hello World vNext repository
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
* Navigate to the src directory
```
cd src/helloworldweb
```
* Run the web application
```
k web
```
* Open a browser and go to http://localhost:5000. If everything is in order, you will see "Hello World" staring back at you ....

    ![Hello World](https://jabbrlive.blob.core.windows.net/jabbr-uploads/clipboard_cf58.png)

Great Success !
 




















