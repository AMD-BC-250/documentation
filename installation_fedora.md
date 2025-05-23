```bash
#!/usr/bin/env bash
set -euo pipefail

if [[ $(id -u) != "0" ]]; then
    echo 'Script must be run as root or with sudo!'
    exit 1
fi

# install patched mesa + block any updates from main repos
echo -n "Adding mesa copr... "
if grep -q Nobara "/etc/system-release"; then
    echo -n "Nobara detected... "
    sed -i '2s/^/exclude=mesa*\n/' /etc/yum.repos.d/nobara.repo
else
    echo -n "Hi, Fedora... "
    sed -i '2s/^/exclude=mesa*\n/' /etc/yum.repos.d/fedora.repo
    sed -i '2s/^/exclude=mesa*\n/' /etc/yum.repos.d/fedora-updates.repo
fi
dnf copr enable mixaill/amd-bc-250 -y
dnf upgrade --refresh -y

# make sure radv_debug option is set in environment
echo -n "Setting RADV_DEBUG option... "
echo 'RADV_DEBUG=nocompute' > /etc/environment

# install segfaults governor
echo "Installing GPU governor... "
dnf install libdrm-devel cmake make g++ git -y
git clone https://gitlab.com/mothenjoyer69/oberon-governor.git && cd oberon-governor
cmake . && make && make install
systemctl enable oberon-governor.service

# make sure amdgpu and nct6683 options are in the modprobe files and update initrd
echo -n "Setting amdgpu module option... "
echo 'options amdgpu sg_display=0' > /etc/modprobe.d/options-amdgpu.conf
echo -n "Setting nct6683 module option... "
echo 'nct6683' > /etc/modules-load.d/99-sensors.conf
echo 'options nct6683 force=true' > /etc/modprobe.d/options-sensors.conf
echo "OK, regenerating initrd (this may take a while)"
dracut --stdlog=4 --regenerate-all --force

# clear nomodeset from /etc/default/grub and update config
echo "Fixing up GRUB config..."
sed -i 's/nomodeset//g' /etc/default/grub
sed -i 's/amdgpu\.sg_display=0//g' /etc/default/grub
grub2-mkconfig -o /etc/grub2.cfg

# install mesa OpenCL
sudo dnf install mesa-libOpenCL --allowerasing

# add amdgpu(for sure)
grubby --update-kernel=ALL --args=amdgpu.sg_display=0

# add mitingation
grubby --update-kernel=ALL --args=mitigations=off

# that should do it
echo "Maybe it all works, maybe it doesn't, anyway, rebooting system in 15 seconds, ctrl-C now to cancel..."
sleep 15 && systemctl reboot
```
