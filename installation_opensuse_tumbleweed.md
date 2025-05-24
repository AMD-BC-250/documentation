# OpenSUSE configuration on AMD BC-250

## Installation script

```bash
# add repo
sudo zypper addrepo -f -p 90 https://download.opensuse.org/repositories/home:mixaill:amd-bc-250/openSUSE_Tumbleweed/home:mixaill:amd-bc-250.repo

# update packages
sudo zypper refresh
sudo zypper dup --allow-vendor-change

# install AMD BC-250 board support package
sudo zypper install amd-bc-250

# allow selinux to run Wine
sudo setsebool -P selinuxuser_execmod 1
sudo setsebool -P selinuxuser_execheap 1
sudo setsebool -P selinuxuser_execstack 1
```

Reboot the system after installation

## GPU tuning

* You can tune GPU Governor in `/etc/amd-bc-250-gpu-governor.yaml` file
