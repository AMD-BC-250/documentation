```bash
#add repos
sudo zypper addrepo -f -p 95 https://ftp.gwdg.de/pub/linux/misc/packman/suse/openSUSE_Tumbleweed packman
sudo zypper addrepo -f -p 90 https://download.opensuse.org/repositories/home:mixaill:amd-bc-250/openSUSE_Tumbleweed/home:mixaill:amd-bc-250.repo

#update packages
sudo zypper refresh
sudo zypper dup --allow-vendor-change

#install -> vulkan
sudo zypper install libvulkan_radeon vulkan-tools

#install -> openCL
sudo zypper install clinfo Mesa-libRusticlOpenCL

#install -> info
sudo zypper install amdgpu_top htop libva-utils

#install -> AMD BC-250 board support packages
sudo zypper install amd-bc-250

#tune -> start GPU governor (config file located in `/etc/amd-bc-250-gpu-governor.yaml`)
sudo systemctl enable amd-bc-250-gpu-governor
sudo systemctl start amd-bc-250-gpu-governor

#tune -> allow selinux to run wine
sudo setsebool -P selinuxuser_execmod 1
sudo setsebool -P selinuxuser_execheap 1
sudo setsebool -P selinuxuser_execstack 1
```
