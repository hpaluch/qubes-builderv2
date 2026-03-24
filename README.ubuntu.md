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
```

Build:
```shell
# verify:
./qb --builder-conf example-configs/qubes-os-r4.3.yml -c builder-rpm -c builder-debian -c qubes-release package fetch
# tested:
./qb --builder-conf example-configs/ubuntu.yml template fetch prep build
```

TDODO: It still fails.

