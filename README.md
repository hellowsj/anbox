<h2 align="center">Anbox - Android in a Box</h2>
<p align="center">
  <img title="Build Status" src="https://travis-ci.org/anbox/anbox.svg?branch=master">
</p>

Anbox is a container-based approach to boot a virtual Android 7.1.1 (nougat) device on a regular GNU/Linux operating system. We use [LXC](https://linuxcontainers.org/) to run a modified [Android Open Source Project](https://source.android.com/) without the overhead of a hypervisor virtualization. 

In other words: Anbox will let you seamlessly run your favourite Android apps on your Linux computer.

&nbsp;
## **Installation**
We are distributing Anbox with [snap](https://snapcraft.io) packages:
```sh
$ sudo snap install --devmode --beta anbox
```
At the moment we require the use of `--devmode` as the Anbox snap is not
yet fully confined. Work has started with the upstream `snapd` project to
get support for full confinement. As a side effect of using `--devmode` the snap will not automatically update.

We officially support **Ubuntu 18.04 (bionic)** and **Ubuntu 20.04 (focal)** as they include everything you need to run Anbox, including the kernel modules.

If you want to build Anbox yourself, take a look at the [Build Instructions](docs/build.md).

&nbsp;
### **Kernel Modules**
Anbox requires [ashmem](https://elinux.org/Android_Kernel_Features#ashmem) and [binder](https://elinux.org/Android_Binder) for the Android subsystem. You can test wether they are available by executing:
```sh
$ sudo modprobe binder_linux
$ sudo modprobe ashmem_linux
```
Ubuntu includes them since version 18.04.4. If your Kernel does not have the modules included for whatever reason, you can use our unmaintained [Kernel Modules](https://github.com/anbox/anbox-modules).


#### **Secure Boot can cause errors when trying to load `ashmem_linux`**
There are two ways around this: 
* One is to disable the SecureBoot: https://wiki.ubuntu.com/UEFI/SecureBoot/DKMS. 
* Following [this post](https://github.com/anbox/anbox/issues/1570), the other way is to sign the `ashmem_linux` kernel module yourself. Note that you may have to enroll your own key, as described [here](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot).
<!-- TODO: mode detailed desctiption of process
  - create separate guide
-->


____
&nbsp;
## **Install and Run Android Applications**
You can install Android applications from the command line using adb.
```sh
adb install xyz.apk
```
The applications you can use with Anbox need to match your host architecture. You cannot use an app made for AArch64/ARM64 on an x86\_64 machine.

You may want to install [F-Droid](https://f-droid.org/) to get applications
graphically. Note that the Google Play Store will not work as is, because it
relies on the proprietary Google Play Services, which are not installed.


____
&nbsp;
## **Documentation**
You will find additional documentation for Anbox in the *docs* subdirectory
of the project source.

Interesting things to have a look at

 * [Runtime Setup](docs/runtime-setup.md)
 * [Build Android image](docs/build-android.md)
 * [Generate Android emugl source](docs/generate-emugl-source.md)
 * [DBUS interface](docs/dbus.md)

&nbsp;
### **Overview**
Anbox uses Linux namespaces (user, pid, uts, net, mount, ipc) to run a
full Android system in a container and provide Android applications on
any GNU/Linux-based platform.

The Android inside the container has no direct access to any hardware.
All hardware access is going through the anbox daemon on the host. We're
reusing what Android implemented within the QEMU-based emulator for OpenGL
ES accelerated rendering. The Android system inside the container uses
different pipes to communicate with the host system and sends all hardware
access commands through these.

For more details have a look at the following documentation pages:

 * [Android Hardware OpenGL ES emulation design overview](https://android.googlesource.com/platform/external/qemu/+/emu-master-dev/android/android-emugl/DESIGN)
 * [Android QEMU fast pipes](https://android.googlesource.com/platform/external/qemu/+/emu-master-dev/android/docs/ANDROID-QEMU-PIPE.TXT)
 * [The Android "qemud" multiplexing daemon](https://android.googlesource.com/platform/external/qemu/+/emu-master-dev/android/docs/ANDROID-QEMUD.TXT)
 * [Android qemud services](https://android.googlesource.com/platform/external/qemu/+/emu-master-dev/android/docs/ANDROID-QEMUD-SERVICES.TXT)

Anbox is currently suited for the desktop use case but can be used on mobile
operating systems like [Ubuntu Touch](https://ubuntu-touch.io/) or
[postmarketOS](https://postmarketos.org)
([installation instructions](https://wiki.postmarketos.org/wiki/Anbox)).
However this is still a work in progress.

The Android runtime environment ships with a minimal customized Android system
image based on the [Android Open Source Project](https://source.android.com/).
The used image is currently based on Android 7.1.1


____
&nbsp;
## **Reporting bugs**
If you have found an issue with Anbox, please [file a bug](https://github.com/anbox/anbox/issues/new).


____
## **Get in Touch**
If you want to get in contact with the developers please feel free to join the
*#anbox* IRC channel on [Freenode](https://freenode.net/).

____
## **Copyright and Licensing**
Anbox reuses code from other projects like the Android QEMU emulator. These
projects are available in the external/ subdirectory with the licensing terms
included.

The Anbox source itself, if not stated differently in the relevant source files,
is licensed under the terms of the GPLv3 license.
