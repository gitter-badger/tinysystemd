# TINYSYSTEMD 7 2015-06-10 228

## NAME

tinysystemd - another way to build systemd for debian

## DESCRIPTION

**tinysystemd** is just a set of debian-specific files that helps to build systemd packages for those who wants to use only service manager from it.

## PACKAGES

**tynysystemd** aims to provide a systemd distribution that divided into small packages. Here is the list of packages:

+ *tinysystemd* - main package, provides systemd itself and other essential tools
+ *libsystemd0* - systemd library
+ *libsystemd-dev* - systemd development library
+ *systemd-sysv* - manual pages and links needed for systemd to replace sysvinit
+ *systemd-logind* - systemd login daemon
+ *libpam-systemd* - systemd PAM library
+ *tinysystemd-utils* - additional systemd utilities, such as *cgls*, *cgtop*, *nspawn* and so on

## SERVICES

**tinysystemd** provides some essential for working operating systems services:

+ *networking.service* - replacement for */etc/init.d/networking* script from *ifupdown* package. Although systemd provides SysV compatibility layer, */etc/init.d/networking* does not work properly, so I've decided to make a "native" service unit for setting up the network.
+ *network-fuckup.service* - brings up network in the rescue mode.
+ *ssh-fuckup.service* - brings up ssh in the rescue mode. Starts after *network-fuckup.service*. Doesn't start if *openssh-server* is not installed.
+ *allow-login.service* - allows user login on systems without *systemd-logind*.
+ *console-setup.service* and *keyboard-setup.service* - native replacements for */etc/init.d/console-setup* and */etc/init.d/keyboard-setup*.

## RENAMINGS

As you know, origininal systemd uses ugly 'systemd-' prefix in every filename of its binaries, except of \*ctl utilites. I think it is quite uncomfortable, so I've decided to provide a new naming scheme. If you want to use **tinysystemd** and you need to use old naming scheme, add command `export PATH=$PATH":/opt/tinysystemd/bin"` to your shell config file. Here is the new naming scheme:

/bin:

    systemd-ask-password            -> ask-password
    systemd-notify                  -> sd-notify
    systemd-machine-id-setup        -> machine-id-setup
    systemd-tty-ask-password-agent  -> tty-ask-password-agent
    systemd-tmpfiles                -> tmpfiles
    systemd-escape                  -> sd-escape

/lib/systemd:

    systemd-ac-power                -> ac-power
    systemd-activate                -> activate
    systemd-bus-proxyd              -> bus-proxyd
    systemd-cgroups-agent           -> cgroups-agent
    systemd-cryptsetup              -> cryptsetup
    systemd-fsck                    -> fscheck
    systemd-initctl                 -> initctl
    systemd-journald                -> journald
    systemd-logind                  -> logind
    systemd-machine-id-commit       -> machine-id-commit
    systemd-modules-load            -> modules-load
    systemd-quotacheck              -> quotacheck
    systemd-remount-fs              -> remount-fs
    systemd-reply-password          -> reply-password
    systemd-shutdown                -> shutdown
    systemd-sleep                   -> sleep
    systemd-socket-proxyd           -> socket-proxyd
    systemd-sysctl                  -> sysctl-bridge
    systemd-sysv-install            -> sysv-install
    systemd-update-done             -> update-done
    systemd-update-utmp             -> update-utmp
    systemd-user-sessions           -> user-sessions

/usr/bin:

    systemd-run                     -> runservice
    systemd-nspawn                  -> nspawn
    systemd-delta                   -> sd-delta
    systemd-analyze                 -> sd-analyze
    systemd-cgls                    -> cgls
    systemd-cgtop                   -> cgtop
    systemd-path                    -> sd-path
    systemd-cat                     -> sd-cat
    systemd-detect-virt             -> detect-virt

Please note that this is not a final renaming scheme. 'sd-' prefix now used as a temporary solution for preventing collisions with existing filenames.

## BUGS

I am not responsible for the bugs in the original systemd. If you want to file a bug that related to systemd behaviour, please go to *https://github.com/systemd/systemd/issues*.

If you want to report a **tinysystemd** packaging issue, please go to *https://github.com/Like-all/tinysystemd/issues*.

## SEE ALSO

systemd(1)
