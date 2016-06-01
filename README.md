# tinysystemd

[![](https://travis-ci.org/Like-all/tinysystemd-substrate.svg?branch=master)](https://travis-ci.org/Like-all/tinysystemd-substrate)
![](https://wasteland.ml/api/badges/current-version/tinysystemd/jessie/amd64?drop-cache=true)

Another way to build systemd for debian

![Tinysystemd logo](/extra/images/logo.png)

#### man 7 tinysystemd

**Please note that tinysystemd is still under heavy testing and may break your operating system.**

News
----

+ **Tinysystemd has reached one year of stable lifetime. Although there are still lot of things to do, I am sure that this thing works. So, I am glad to announce a [new branch](https://github.com/Like-all/tinysystemd/tree/ubuntu-xenial) for upcoming Ubuntu 16.04 Xenial Xerus.**
+ **Starting from Debian 9.0 "Stretch" I am going to stick to stable systemd version from the Debian git repo. Backporting is hard, it takes a lot of time and may lead to unexpected behaviour of operating system.**

Trying tinysystemd using vagrant
--------------------------------

From now you can easily spin up a box with the latest stable version of `tinysystemd` using `vagrant`. Just clone this repo and do the following:

    cd tinysystemd/extra/vagrant
    vagrant up
    vagrant ssh

Installing
----------

First, add a repo to your sources:

    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys "8C2C65DE"
    echo "deb http://crapcannon.tk/debian/ jessie main contrib non-free" | sudo tee /etc/apt/sources.list.d/tinysystemd.list
    sudo apt-get update

Second, you need to switch to traditional `init` and then reboot using it(otherwise you will get an error):

    sudo apt-get install sysvinit-core #(or sudo apt-get purge systemd-sysv)
    sudo reboot

Finally, remove `systemd` and install `tinysystemd` and `libsystemd0`:

    sudo apt-get purge systemd
    apt-get install tinysystemd libsystemd0

If you want to boot usnig `tinysystemd` by default, you will need to install `systemd-sysv`:

    sudo apt-get install systemd-sysv

Building
--------

At first, you need to clone vanilla `systemd` from upstream repo:

    git clone https://github.com/systemd/systemd.git

At second, you need to set HEAD to any release tag you want to use. Note that master branch now built around v226:

    mv systemd/ systemd-229
    cd systemd-229/
    git reset --hard v229

At third, you need to pull this repo as a submodule:

    git submodule add https://github.com/Like-all/tinysystemd.git debian
    cd debian && git reset --hard v229

At fourth, you will need to install build dependencies:

    mk-build-deps --install control

Finally, create the tarball with original source code and run `debuild`:

    cd ..
    dh_make --createorig
    debuild -us -uc

Feel free to add any CI helper stuff you want to use to this repo.

PAQ(Preventively Answered Questions)
------------------------------------

###Why?

Because I fed up with all of these wonders provided by the `systemd` project. The aim of this repo is to provide things *you only need* from origin systemd. Also it aims to avoid stupid things [like this one]().

There are some additional services provided by `tinysystemd`:

+ *networking.service* - replacement for */etc/init.d/networking* script from *ifupdown* package. Although systemd provides SysV compatibility layer, */etc/init.d/networking* does not work properly, so I've decided to make a "native" service unit for setting up the network.
+ *network-fuckup.service* - brings up network in the rescue mode.
+ *ssh-fuckup.service* - brings up ssh in the rescue mode. Starts after *network-fuckup.service*. Doesn't start if *openssh-server* is not installed.
+ *allow-login.service* - allows user login on systems without *systemd-logind*.
+ *console-setup.service* and *keyboard-setup.service* - native replacements for */etc/init.d/console-setup* and */etc/init.d/keyboard-setup*.

###What about uselessd?

After ownership change in January 2015 uselessd looks like abandonware. I think it might lead to compatibility problems(see merging systemd helper libraries into one libsystemd0).

###Where is udev?

For now, systemd-229 works fine with shipping in jessie udev-215 and I don't see any reason to additionally maintain tons of debian-specific kludges around it. However, this can break when ~~kdbus~~ ~~bus-1~~ some unpredictable dangerous piece of shit will come.

###Where is systemd-cgls, systemd-cgtop, systemd-analyze, etc?

These tools are not necessary to keep my system up and running. However, if you want to use them, you may install `tinysystemd-utils` package.

###Where is systemd-logind, systemd-hostnamed, systemd-timedated, systemd-blowjobd, etc?

For some reasons(I really don't know why) few essential packages, such as `qemu-kvm`, depend on `policykit-1` in jessie. And `policykit-1` depends on `libpam-systemd`, which depends on `systemd` that contains `systemd-logind`. I'm really tired of that shit, but I also want to provide a working solution for current Debian release, so I've decided to move `systemd-logind` into separated package. What about other fancy daemons? Currently I don't package them, but recently I made it possible to inject dependencies into other packages, so probably soon you will be able to launch your shiny-new desktop environments under `tinysystemd`.

###Why did you rename all of the binaries?

Because that ugly `systemd-` prefix breaks my shell completion. For example, if I want to invoke `systemd-run <something>`, I need to:

+ Type `sy`, then press `<TAB>`
+ Yell **SHIT**, because completion affects my favourite mail client, `sylpheed`
+ Type `s`, then press `<TAB>`
+ Yell **SHIT**, because completion affects `sysctl`
+ Type `tem`, then press `<TAB>`
+ Yell **SHIT**, because completion affects `systemctl`
+ Type `d`, then press `<TAB>`
+ Yell **SHIT**, because completion affects all binaries with `systemd-` prefix
+ Type `r`, then press `<TAB>`
+ *Actually* use `systemd-run`

Instead of that mess, I just invoke `runservice`. That's easy.

Please, read `man 7 tinysystemd` if you want to read about the new naming scheme.

And finally, if you **really** want to get some compatibility, you may add command `export PATH=$PATH":/opt/tinysystemd/bin"` to your shell config file.

###What about graphical desktop?

It works:

![](http://wasteland.it-the-drote.tk/shot/debian/tinysystemd.png)

### Please make an ebuild, RPM spec, pkgbuild, whatever

I'm not interested in maintaining tinysystemd on distributions that I don't use. Not my business.
