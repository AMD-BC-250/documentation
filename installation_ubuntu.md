# Ubuntu on AMD BC-250

## Installation script

```bash
#!/bin/bash

if [ "$(id -u)" -ne 0 ]; then
    echo "error: script must be running with root privileges."
    exit 1
fi

check_error() {
    if [ $? -ne 0 ]; then
        echo "error: $1"
        exit 1
    fi
}

if ! command -v git &>/dev/null; then
    apt update
    check_error "failed to update package repositories"
    apt install -y git
    check_error "fauked to install git"
fi

git ls-remote --exit-code https://gitlab.com/mothenjoyer69/oberon-governor.git &>/dev/null
if [ $? -ne 0 ]; then
    echo "error: oberon-governor repo is not accessible"
    exit 1
fi

sudo apt update
check_error "failed to update package repositories"

sudo apt install -y git cmake make g++ build-essential
check_error "failed to install c++ compiler"

sudo apt install -y libdrm-dev libdrm2 libdrm-common libpciaccess0 pkg-config
check_error "failed to install libdrm development libraries"

sudo apt upgrade -y
check_error "failed to upgrade packages"

if [ -d "oberon-governor" ]; then
    rm -rf oberon-governor
    git clone https://gitlab.com/mothenjoyer69/oberon-governor.git
    check_error "failed to clone repo"
else
    git clone https://gitlab.com/mothenjoyer69/oberon-governor.git
    check_error "failed to clone repo"
fi

cd oberon-governor || { echo "error: failed to find oberon-governor dir"; exit 1; }

mkdir -p build
cd build || { echo "error: failed to open build dir"; exit 1; }

cmake ..
check_error "failed to configure cmake"

make -j$(nproc)
check_error "failed to build"

sudo make install
check_error "failed to install"

sudo systemctl status oberon-governor.service --no-pager

if ! systemctl is-active --quiet oberon-governor.service; then
    sudo systemctl enable oberon-governor.service
    sudo systemctl start oberon-governor.service
    sudo systemctl status oberon-governor.service --no-pager
fi

sudo apt install -y software-properties-common
sudo add-apt-repository -y ppa:ernstp/mesarc
check_error "failed to add mesa repo"

sudo apt update
check_error "failed to update packages"

sudo apt upgrade -y
check_error "failed to upgrade packages"

sudo apt install -y mesa-utils
check_error "failed to install mesa-utils"

glxinfo | grep "OpenGL version"

if glxinfo | grep "OpenGL version" | grep -q "25.1"; then
    echo "mesa 25.1 was found"
else
    echo "WARN: mesa 25.1 was not found"
fi
```
