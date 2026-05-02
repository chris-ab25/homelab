## 🚀 Helm

Follow the steps below to install and configure helm on your system.

### How to install
```bash
wget https://get.helm.sh/helm-v4.1.4-linux-amd64.tar.gz
tar -xzvf helm-v4.1.4-linux-amd64.tar.gz
cd linux-amd64
cp helm /usr/local/bin/
```

### Verify helm install
```bash
[root@fedora ~]# helm version
version.BuildInfo{Version:"v4.1.4", GitCommit:"05fa37973dc9e42b76e1d2883494c87174b6074f", GitTreeState:"clean", GoVersion:"go1.25.9", KubeClientVersion:"v1.35"}
[root@fedora ~]#
```

Helm release page: https://github.com/helm/helm/releases/