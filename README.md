# tinysystemd

![](http://wasteland.it-the-drote.tk/apps/debian/images/tinysystemd-version.png?drop-cache=true) ![](http://wasteland.it-the-drote.tk/apps/debian/images/tinysystemd-lintian-errors.png) ![](http://wasteland.it-the-drote.tk/apps/debian/images/tinysystemd-lintian-warnings.png)

Another way to build systemd for debian

**Please, note that tinysystemd is still under heavy testing and may break your boot**

Installing
----------

First, add a repo to your sources:

    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys "8C2C65DE"
    echo "deb http://wasteland.it-the-drote.tk/apps/debian/ jessie main contrib non-free" | sudo tee /etc/apt/sources.list.d/wasteland.it-the-drote.list
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

At second, you need to set HEAD to any release tag you want to use. Note that master branch now built around v219:

    mv systemd/ systemd-219
    cd systemd-219/
    git reset --hard v219

At third, you need to pull this repo as a submodule:

    git submodule add https://github.com/Like-all/tinysystemd.git debian

Finally, create the tarball with original source code and run `debuild`:

    dh_make --createorig
    debuild -us -uc

Feel free to add any CI helper stuff you want to use to this repo.

PAQ(Preventively Answered Questions)
------------------------------------

###Why?

Because fuck you, that's why.

###Seriously, why?

Because I don't need all of these wonders provided by `systemd` project. I only want to use service manager from it. I also turned off binary logging.

###What about uselessd?

After ownership change in January 2015 uselessd looks like abandonware. I think it might lead to compatibility problems(see merging systemd helper libraries into one libsystemd0).

###Where is udev?

For now, systemd-219 works fine with shipping in jessie udev-215 and I don't see any reason to additionally maintain tons of debian-specific kludges around it. However, this can break when kdbus will come.

###Where is systemd-cgls, systemd-cgtop, systemd-analyze, etc?

These tools are not necessary to keep my system up and running. However, if you want to use them, you may install `tinysystemd-utils` package.

###Where is systemd-logind, systemd-hostnamed, systemd-timedated, systemd-blowjobd, etc?

Why did you came there if you're happy with all of these tools that already shipped with standard systemd package in Debian? Go away and jerk off your shiny new GNOME somewhere else.

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

And no, I don't want to make symlink-spaghetti in my package.

###What about graphical desktop?

It works:

![](http://wasteland.it-the-drote.tk/shot/debian/tinysystemd.png)

### Please make an ebuild, RPM spec, pkgbuild, whatever

I'm not interested in maintaining tinysystemd on distributions that I don't use. Not my business.
