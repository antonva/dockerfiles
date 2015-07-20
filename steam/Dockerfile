FROM tianon/steamos
MAINTAINER Anton Vilhelm Ásgeirsson <anton.v.asgeirsson@gmail.com>

# Setup
ENV TERM xterm
RUN apt-get update && apt-get install -yq steam-launcher

# add the sources.list stuff that steam will at first start
RUN echo 'deb [arch=amd64,i386] http://repo.steampowered.com/steam precise steam' > /etc/apt/sources.list.d/steam.list && \
    dpkg --add-architecture i386

# let's head off a few of the things steam will want to install immediately
# Kmod and wget are there for NVIDIA installation.
RUN apt-get update && \
    apt-get install -yq \
    build-essential \
    kmod \
    wget \
    libgl1-mesa-dri:i386 \
    libgl1-mesa-glx:i386 \
    libc6:i386

# Purge nvidia/nouveau
RUN apt-get -y --purge remove \
    xserver-xorg-video-nouveau \
    nvidia-kernel-common \
    nvidia-kernel-dkms \
    nvidia-glx \
    nvidia-smi \
    nvidia-*

# Disable nouveau driver in modprobe
RUN touch /etc/modprobe.d/disable-nouveau.conf && \
    echo "#Disable nouveau\nblacklist nouveau\noptions nouveau modeset=0" \
    >> /etc/modprobe.d/disable-nouveau.conf

# Install Nvidia drivers
WORKDIR /tmp
RUN wget http://us.download.nvidia.com/XFree86/Linux-x86_64/352.21/NVIDIA-Linux-x86_64-352.21.run  && \
    chmod +x NVIDIA-Linux-x86_64-352.21.run && \
    /tmp/NVIDIA-Linux-x86_64-352.21.run \
    -N \
    --silent \
    --no-kernel-module
    
# steam itself needs to be able to install things, and it uses "sudo" for that
RUN apt-get install -yq sudo
RUN echo 'steam ALL = NOPASSWD: ALL' > /etc/sudoers.d/steam && chmod 0440 /etc/sudoers.d/steam

RUN adduser --disabled-password --gecos 'Steam' steam && \
    adduser steam video
USER steam
ENV HOME /home/steam

CMD ["steam"]
