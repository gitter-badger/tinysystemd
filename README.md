# tinysystemd

![](https://wasteland.ml/api/badges/current-version/tinysystemd/jessie/amd64?drop-cache=true)

Another way to build systemd for debian

#### man 7 tinysystemd

**Please, note that tinysystemd is still under heavy testing and may break your boot**

**Systemd v221 released few days ago and requires swapon(8) from util-linux >= 2.26, which is absent in Jessie. I'll make backport as soon as I can. Shit...**

Installing
----------

First, add a repo to your sources:

    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys "8C2C65DE"
    echo "deb http://wasteland.ml/debian/ jessie main contrib non-free" | sudo tee /etc/apt/sources.list.d/wasteland.it-the-drote.list
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

At first, you need to clone vanilla `systemd` from freedesktop repo:

    git clone http://anongit.freedesktop.org/git/systemd/systemd.git

At second, you need to set HEAD to any release tag you want to use. Note that master branch now built around v220:

    mv systemd/ systemd-220
    cd systemd-220/
    git reset --hard v220

At third, you need to pull this repo as a submodule:

    git submodule add https://github.com/Like-all/tinysystemd.git debian

At fourth, you will need to install build dependencies:

    sudo apt-get build-dep systemd
    sudo apt-get install ruby-md2man #you can get it from my repo

Finally, create the tarball with original source code and run `debuild`:

    dh_make --createorig
    debuild -us -uc

Feel free to add any CI helper stuff you want to use to this repo.

PAQ(Preventively Answered Questions)
------------------------------------

###Why?

Because I fed up with all of these wonders provided by the `systemd` project. I only want to use service manager from it.

There are some additional services provided by `tinysystemd`:

+ *networking.service* - replacement for */etc/init.d/networking* script from *ifupdown* package. Although systemd provides SysV compatibility layer, */etc/init.d/networking* does not work properly, so I've decided to make a "native" service unit for setting up the network.
+ *network-fuckup.service* - brings up network in the rescue mode.
+ *ssh-fuckup.service* - brings up ssh in the rescue mode. Starts after *network-fuckup.service*. Doesn't start if *openssh-server* is not installed.

###What about uselessd?

After ownership change in January 2015 uselessd looks like abandonware. I think it might lead to compatibility problems(see merging systemd helper libraries into one libsystemd0).

###Where is udev?

For now, systemd-220 works fine with shipping in jessie udev-215 and I don't see any reason to additionally maintain tons of debian-specific kludges around it. However, this can break when kdbus will come.

###Where is systemd-cgls, systemd-cgtop, systemd-analyze, etc?

These tools are not necessary to keep my system up and running. However, if you want to use them, you may install `tinysystemd-utils` package.

###Where is systemd-logind, systemd-hostnamed, systemd-timedated, systemd-blowjobd, etc?

For some reasons(I really don't know why) few essential packages, such as `qemu-kvm`, depend on `policykit-1` in jessie. And `policykit-1` depends on `libpam-systemd`, which depends on `systemd` that contains `systemd-logind`. I'm really tired of that shit, but I also want to provide a working solution for current Debian release, so I've decided to move `systemd-logind` into separated package. What about other fancy daemons? I don't need them at all. Modern desktop environments *are not supported by `tinysystemd`*.

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

And finally, if you **really** want to get some compatibility, you may install `tinysystemd-compat` and `tinysystemd-utils-compat` packages.

###What about graphical desktop?

It works:

![](http://wasteland.it-the-drote.tk/shot/debian/tinysystemd.png)

### Please make an ebuild, RPM spec, pkgbuild, whatever

I'm not interested in maintaining tinysystemd on distributions that I don't use. Not my business.
