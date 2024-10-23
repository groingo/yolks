FROM --platform=$TARGETOS/$TARGETARCH debian:bookworm-slim

LABEL author="QuintenQVD" maintainer="josdekurk@gmail.com"

ENV DEBIAN_FRONTEND=noninteractive

# Update base packages and install essential dependencies
RUN apt update && apt upgrade -y && \
    apt install -y --no-install-recommends \
        wget curl git gnupg2 ca-certificates \
        libc++-dev libc6 libfontconfig libicu72 icu-devtools \
        libunwind8 libssl-dev sqlite3 libsqlite3-dev \
        libmariadb-dev libmariadb-dev-compat libz3-dev \
        rapidjson-dev tzdata ffmpeg build-essential cmake \
        libgdiplus libstdc++6 && \
    apt clean && rm -rf /var/lib/apt/lists/*

# Enable i386 architecture support
RUN dpkg --add-architecture i386 && apt update

# Install essential i386 libraries if available
RUN apt install -y --no-install-recommends \
        libc6:i386 libncurses5:i386 libncurses6:i386 && \
    apt clean && rm -rf /var/lib/apt/lists/* || true

# Add WineHQ repository
RUN mkdir -pm755 /etc/apt/keyrings && \
    wget -O /etc/apt/keyrings/winehq-archive.key https://dl.winehq.org/wine-builds/winehq.key && \
    echo "deb [signed-by=/etc/apt/keyrings/winehq-archive.key] https://dl.winehq.org/wine-builds/debian/ bookworm main" > /etc/apt/sources.list.d/winehq.list && \
    apt update

# Install Wine and wine-binfmt
RUN apt install -y --install-recommends winehq-stable wine32 wine64 && \
    apt install -y wine-binfmt && \
    apt clean && rm -rf /var/lib/apt/lists/*

# Install Winetricks
RUN wget -q -O /usr/sbin/winetricks https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks && \
    chmod +x /usr/sbin/winetricks

# Install Box64 for AMD64 architecture
RUN wget -O /etc/apt/sources.list.d/box64.list https://ryanfortner.github.io/box64-debs/box64.list && \
    wget -qO- https://ryanfortner.github.io/box64-debs/KEY.gpg | gpg --dearmor -o /etc/apt/trusted.gpg.d/box64-debs-archive-keyring.gpg && \
    apt update && \
    apt install -y box64-rpi4arm64 && \
    apt clean && rm -rf /var/lib/apt/lists/*

# Configure locale
RUN apt update && apt install -y locales && \
    echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen && \
    locale-gen && \
    update-locale LANG=en_US.UTF-8

# Set up user
RUN useradd -d /home/container -m container
USER container
ENV USER=container HOME=/home/container
WORKDIR /home/container
# Set up Wine environment variables
ENV WINEPREFIX=/home/container/.wine
ENV WINEDLLOVERRIDES="mscoree,mshtml="
ENV DISPLAY=:0
ENV DISPLAY_WIDTH=1024
ENV DISPLAY_HEIGHT=768
ENV DISPLAY_DEPTH=16
ENV AUTO_UPDATE=1
ENV XVFB=1

# Copy entrypoint script
COPY ./entrypoint.sh /entrypoint.sh
CMD ["/bin/bash", "/entrypoint.sh"]
