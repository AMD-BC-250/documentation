```bash
#add repos
sudo zypper addrepo -f -p 99 https://download.opensuse.org/repositories/home:/shundhammer:/myrlyn-git/openSUSE_Tumbleweed/home:shundhammer:myrlyn-git.repo
sudo zypper addrepo -f -p 95 https://ftp.gwdg.de/pub/linux/misc/packman/suse/openSUSE_Tumbleweed packman
sudo zypper addrepo -f -p 94 https://download.opensuse.org/repositories/science:/GPU:/ROCm/openSUSE_Factory/science:GPU:ROCm.repo
sudo zypper addrepo -f -p 90 https://download.opensuse.org/repositories/home:mixaill:amd-bc-250/openSUSE_Tumbleweed/home:mixaill:amd-bc-250.repo

#update packages
sudo zypper refresh
sudo zypper dup --allow-vendor-change

#install -> openCL
sudo zypper install Mesa-libRusticlOpenCL

#install -> info
sudo zypper install amdgpu_top btop clinfo hardinfo2 htop libva-utils

#install -> utils
sudo zypper install myrlyn

#install -> benchmarking
sudo zypper install glmark2 vkmark

#install -> multimedia
sudo zypper install ffmpeg vlc

#install -> GPU governor
sudo zypper install amd-bc-250
sudo systemctl enable amd-bc-250-gpu-governor
sudo systemctl start amd-bc-250-gpu-governor

#tune selinux
sudo setsebool -P selinuxuser_execmod 1
sudo setsebool -P selinuxuser_execheap 1
sudo setsebool -P selinuxuser_execstack 1
```
