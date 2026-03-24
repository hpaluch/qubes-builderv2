# How to build Ubuntu Qube

TODO:

Requires:
- docker executor (each template could use different Executor - you need to check it first!)
- follow main [README.md](README.md) for instructions how to install Docker
- add yourself (in Template VM) to Docker group: `sudo usermod -aG docker $(id -un)`

Preparation in Template VM:
```shell
# you may need to copy dependencies-fedora.txt from this git repo to your template first
# and then:
sudo dnf install $(cat dependencies-fedora.txt)
```

Preparation:
```shell
cd
git clone https://github.com/hpaluch/qubes-builderv2.git
cd qubes-builderv2
git submodule update --init
sudo docker build -f dockerfiles/ubuntu.Dockerfile -t qubes-builder-ubuntu:latest .
# make backup - because /var/lib/docker will not persist reboot:
skopeo copy docker-daemon:qubes-builder-ubuntu:latest oci-archive:$HOME/qubes-builder-ubuntu-latest.tar
```

Build:
```shell
# verify:
./qb --builder-conf example-configs/qubes-os-r4.3.yml -c builder-rpm -c builder-debian -c qubes-release package fetch
# some error - creates .deb packages required by next step
./qb --builder-conf example-configs/ubuntu.yml  package fetch prep build
# next:
./qb --builder-conf example-configs/ubuntu.yml template fetch prep build
```

It fails on mounting already mounted /sys, trying quick fix:
```diff
--- ./artifacts/sources/builder-debian/template_debian/distribution.sh.orig	2026-03-24 20:32:54.556933348 +0100
+++ ./artifacts/sources/builder-debian/template_debian/distribution.sh	2026-03-24 20:34:15.134542453 +0100
@@ -143,7 +143,7 @@
 
     mount -t tmpfs none "${INSTALL_DIR}/run"
     mount -t proc proc "${INSTALL_DIR}/proc"
-    mount -t sysfs sys "${INSTALL_DIR}/sys"
+    [ -d "${INSTALL_DIR}/sys/bus" ] || mount -t sysfs sys "${INSTALL_DIR}/sys"
     createDbusUuid
     addDivertPolicy
 }
```

Now it failes with:
```
20:57:16 [qb.template.jammy.prep] E: Unable to locate package qubes-vm-dependencies
20:57:16 [qb.template.jammy.prep] E: Unable to locate package qubes-vm-recommended

```
