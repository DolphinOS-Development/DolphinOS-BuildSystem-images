FROM archlinux:latest

LABEL org.opencontainers.image.source="https://github.com/DolphinOS-Development/DolphinOS-BuildSystem-images"
LABEL org.opencontainers.image.description="Arch Linux builder image for DolphinOS installation ISO repository"
LABEL org.opencontainers.image.licenses="GPL-3.0"

# Avoid interactive pacman prompts
ENV TERM=xterm
ENV LANG=C.UTF-8

# Update system and install base build tools
RUN echo -e "[dolphinos-repo]\nSigLevel = Optional TrustedOnly\nServer = https://dolphinos-development.github.io/dolphinos-repo/x86_64" >> /etc/pacman.conf \
    && pacman -Syu --noconfirm \
    && pacman -S --noconfirm --needed sudo git base-devel reflector archiso mkinitcpio-archiso squashfs-tools wget base-devel curl lftp networkmanager rsync openssh libspng grub memtest86+-efi efibootmgr \
    && reflector --latest 20 --protocol https --sort rate --save /etc/pacman.d/mirrorlist \
    && pacman -Syu --noconfirm \
    && pacman -Scc --noconfirm

# Create non-root build user
RUN useradd builduser -m \
    && passwd -d builduser \
    && echo "builduser ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers \
    && mkdir -p /buildworkdir \
    && chown -R builduser:builduser /buildworkdir

# Install yay for AUR support
USER builduser
WORKDIR /buildworkdir
RUN git clone https://aur.archlinux.org/yay-bin.git \
    && cd yay-bin \
    && makepkg -si --noconfirm \
    && cd .. && rm -rf yay-bin

# Default workdir for builds
WORKDIR /buildworkdir

# Switch to root by default
USER root
