FROM archlinux:latest

LABEL org.opencontainers.image.source="https://github.com/DolphinOS-Development/DolphinOS-BuildSystem-images"
LABEL org.opencontainers.image.description="Arch Linux builder image for DolphinOS distro repo"
LABEL org.opencontainers.image.licenses="GPL-3.0"

# Avoid interactive pacman prompts
ENV TERM=xterm
ENV LANG=C.UTF-8

# Update system and install base build tools
RUN pacman -Syu --noconfirm \
    && pacman -S --noconfirm --needed sudo git base-devel namcap \
    && pacman -Scc --noconfirm

# Create non-root build user
RUN useradd builduser -m \
    && passwd -d builduser \
    && echo "builduser ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers \
    && mkdir -p /build \
    && chown -R builduser:builduser /build

# Set up Actions work directory to be owned by the builduser
RUN mkdir -p /__w && chown -R builduser:builduser /__w

# Install yay for AUR support
USER builduser
WORKDIR /build
RUN git clone https://aur.archlinux.org/yay-bin.git \
    && cd yay-bin \
    && makepkg -si --noconfirm \
    && cd .. && rm -rf yay-bin

# Default workdir for builds
WORKDIR /build

# Switch builduser by default
USER builduser
