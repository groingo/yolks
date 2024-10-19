FROM        --platform=$TARGETOS/$TARGETARCH debian:bookworm-slim

LABEL       author="QuintenQVD" maintainer="josdekurk@gmail.com"

ENV     DEBIAN_FRONTEND noninteractive

## Update base packages
RUN          apt update \
             && apt upgrade -y

## Install dependencies
RUN          apt install -y libc++-dev libc6 git wget curl tar zip unzip binutils xz-utils liblzo2-2 cabextract iproute2 net-tools libatomic1 libsdl1.2debian libsdl2-2.0-0 \
             libfontconfig libicu72 icu-devtools libunwind8 libssl-dev sqlite3 libsqlite3-dev libmariadb-dev libmariadb-dev-compat libduktape207 locales ffmpeg gnupg2 apt-transport-https software-properties-common ca-certificates \
             libz3-dev rapidjson-dev tzdata libevent-dev libzip4 libsdl2-mixer-2.0-0 libsdl2-image-2.0-0 build-essential cmake libgdiplus libstdc++6
			 
## Configure locale
RUN          update-locale lang=en_US.UTF-8 \
             && dpkg-reconfigure --frontend noninteractive locales

## Install Wine (box64 for amd64, box86 for i386)
RUN          apt update \
             && apt install -y wget dpkg


##Install box64
RUN         wget https://ryanfortner.github.io/box64-debs/box64.list -O /etc/apt/sources.list.d/box64.list \
            && wget -qO- https://ryanfortner.github.io/box64-debs/KEY.gpg | gpg --dearmor -o /etc/apt/trusted.gpg.d/box64-debs-archive-keyring.gpg  \
            && apt update && apt install box64-rpi4arm64 -y
# User-defined Wine version variables
ENV branch="devel"
ENV version="9.19"
ENV id="debian"
ENV dist="bullseye"
ENV tag="-1"

# Download and Install amd64 (Wine for Box64)
RUN          wget -q https://dl.winehq.org/wine-builds/${id}/dists/${dist}/main/binary-amd64/wine-${branch}-amd64_${version}~${dist}${tag}_amd64.deb \
             && wget -q https://dl.winehq.org/wine-builds/${id}/dists/${dist}/main/binary-amd64/wine-${branch}_${version}~${dist}${tag}_amd64.deb \
             && dpkg-deb -x wine-${branch}-amd64_${version}~${dist}${tag}_amd64.deb wine-installer \
             && dpkg-deb -x wine-${branch}_${version}~${dist}${tag}_amd64.deb wine-installer

RUN          mv wine-installer/opt/wine* ~/wine

# Enable multi-architecture and install wine dependencies
RUN          sudo dpkg --add-architecture armhf \
             && sudo apt-get update \
             && sudo apt-get install -y libasound2:armhf libc6:armhf libglib2.0-0:armhf libgphoto2-6:armhf libgphoto2-port12:armhf \
             libgstreamer-plugins-base1.0-0:armhf libgstreamer1.0-0:armhf libldap-2.4-2:armhf libopenal1:armhf libpcap0.8:armhf \
             libpulse0:armhf libsane1:armhf libudev1:armhf libusb-1.0-0:armhf libvkd3d1:armhf libx11-6:armhf libxext6:armhf \
             libasound2-plugins:armhf ocl-icd-libopencl1:armhf libncurses6:armhf libncurses5:armhf libcap2-bin:armhf libcups2:armhf \
             libdbus-1-3:armhf libfontconfig1:armhf libfreetype6:armhf libglu1-mesa:armhf libglu1:armhf libgnutls30:armhf \
             libgssapi-krb5-2:armhf libkrb5-3:armhf libodbc1:armhf libosmesa6:armhf libsdl2-2.0-0:armhf libv4l-0:armhf \
             libxcomposite1:armhf libxcursor1:armhf libxfixes3:armhf libxi6:armhf libxinerama1:armhf libxrandr2:armhf \
             libxrender1:armhf libxxf86vm1:armhf

# Install amd64 dependencies for Wine on ARM64
RUN          sudo apt-get install -y libasound2:arm64 libc6:arm64 libglib2.0-0:arm64 libgphoto2-6:arm64 libgphoto2-port12:arm64 \
             libgstreamer-plugins-base1.0-0:arm64 libgstreamer1.0-0:arm64 libldap-2.4-2:arm64 libopenal1:arm64 libpcap0.8:arm64 \
             libpulse0:arm64 libsane1:arm64 libudev1:arm64 libunwind8:arm64 libusb-1.0-0:arm64 libvkd3d1:arm64 libx11-6:arm64 libxext6:arm64 \
             ocl-icd-libopencl1:arm64 libasound2-plugins:arm64 libncurses6:arm64 libncurses5:arm64 libcups2:arm64 \
             libdbus-1-3:arm64 libfontconfig1:arm64 libfreetype6:arm64 libglu1-mesa:arm64 libgnutls30:arm64 \
             libgssapi-krb5-2:arm64 libjpeg62-turbo:arm64 libkrb5-3:arm64 libodbc1:arm64 libosmesa6:arm64 libsdl2-2.0-0:arm64 libv4l-0:arm64 \
             libxcomposite1:arm64 libxcursor1:arm64 libxfixes3:arm64 libxi6:arm64 libxinerama1:arm64 libxrandr2:arm64 \
             libxrender1:arm64 libxxf86vm1:arm64

# Install additional dependencies for wine-staging
RUN          sudo apt install libstb0 -y \
             && cd ~/Downloads \
             && wget -r -l1 -np -nd -A "libfaudio0_*~bpo10+1_i386.deb" http://ftp.uk.debian.org/debian/pool/main/f/faudio/ \
             && dpkg-deb -xv libfaudio0_*~bpo10+1_i386.deb libfaudio \
             && sudo cp -TRv libfaudio/usr/ /usr/ \
             && rm libfaudio0_*~bpo10+1_i386.deb \
             && rm -rf libfaudio

# Install symlinks for wine binaries
RUN          sudo ln -s ~/wine/bin/wine /usr/local/bin/wine \
             && sudo ln -s ~/wine/bin/wine64 /usr/local/bin/wine64 \
             && sudo ln -s ~/wine/bin/wineboot /usr/local/bin/wineboot \
             && sudo ln -s ~/wine/bin/winecfg /usr/local/bin/winecfg \
             && sudo ln -s ~/wine/bin/wineserver /usr/local/bin/wineserver

RUN		useradd -d /home/container -m container
USER    container
ENV     USER=container HOME=/home/container
WORKDIR /home/container

COPY    ./entrypoint.sh /entrypoint.sh
CMD     ["/bin/bash", "/entrypoint.sh"]
