# My Fedora Setup

## Main Install

```bash
# RPM Fusion
sudo dnf install https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm

# Don't use the RPM Fusion Mirror, as sometimes it can pick a mirror which is down
sudo sed -i -E '/^ *metalink=/s/^/# /;/^ *# *baseurl=/s/^# *//' /etc/yum.repos.d/rpmfusion-*.repo

# 1Password Repo
sudo rpm --import https://downloads.1password.com/linux/keys/1password.asc
sudo sh -c 'echo -e "[1password]\nname=1Password Stable Channel\nbaseurl=https://downloads.1password.com/linux/rpm/stable/\$basearch\nenabled=1\ngpgcheck=1\nrepo_gpgcheck=1\ngpgkey=\"https://downloads.1password.com/linux/keys/1password.asc\"" > /etc/yum.repos.d/1password.repo'

# VS Code Repo
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\nautorefresh=1\ntype=rpm-md\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" | sudo tee /etc/yum.repos.d/vscode.repo > /dev/null

# Update and Upgrade
sudo dnf update


# Main Tools
sudo dnf install @development-tools htop btop wget curl xclip rust cargo openssl-devel libyaml-devel zlib-devel gmp-devel mysql-devel make automake gcc gcc-c++ kernel-devel ripgrep vips

# Google Chrome (once extra repos are enabled)
sudo dnf install google-chrome-stable

# Podman (podman-docker provides a `docker` command that runs podman)
sudo dnf install podman podman-compose podman-docker
sudo touch /etc/containers/nodocker

# Docker-compatible API socket, for tools that talk to Docker
systemctl --user enable --now podman.socket
echo 'export DOCKER_HOST=unix://$XDG_RUNTIME_DIR/podman/podman.sock' >> ~/.zshrc

# Start containers on boot
systemctl --user enable podman-restart.service
loginctl enable-linger dean

cat <<'EOF' | sudo tee /opt/docker-compose.yml > /dev/null
services:
  mysql:
    image: docker.io/library/mysql:8.4
    container_name: mysql
    ports:
      - "127.0.0.1:3306:3306"
    environment:
      MYSQL_ALLOW_EMPTY_PASSWORD: "true"
    volumes:
      - mysql-data:/var/lib/mysql
    restart: always
  redis:
    image: docker.io/library/redis:8
    container_name: redis
    ports:
      - "127.0.0.1:6379:6379"
    restart: always

volumes:
  mysql-data:
EOF

podman compose -f /opt/docker-compose.yml up -d

# Mise
sudo dnf copr enable jdxcode/mise
sudo dnf install -y mise

mise use -g ruby@4
mise use -g claude@latest
mise use -g codex@latest
mise use -g opencode@latest

# Zsh
sudo dnf install zsh -y

# Oh my Zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Starship
curl -sS https://starship.rs/install.sh | sh
echo 'eval "$(starship init zsh)"' >> ~/.zshrc

# Try
gem install try-cli
echo 'eval "$(try init ~/code/tries)"' >> ~/.zshrc

# Zsh Plugin
git clone git@github.com:deanpcmad/zsh-plugin.git ~/.oh-my-zsh/custom/plugins/deanpcmad
# Then edit .zshrc to set plugins. The git plugin isn't needed: plugins=(mise deanpcmad)

ln -s ~/.oh-my-zsh/custom/plugins/deanpcmad/gitconfig ~/.gitconfig
ln -s ~/.oh-my-zsh/custom/plugins/deanpcmad/gitignore ~/.gitignore
ln -s ~/.oh-my-zsh/custom/plugins/deanpcmad/irbrc ~/.irbrc
ln -s ~/.oh-my-zsh/custom/plugins/deanpcmad/gemrc ~/.gemrc

# Reload Zsh
source ~/.zshrc

# Restic
sudo dnf install restic

# Nextcloud
sudo dnf install nextcloud-client

# Others inc Discord from the RPM Fusion Repo
sudo dnf install discord
flatpak install com.spotify.Client
flatpak install md.obsidian.Obsidian

# 1Password
sudo dnf install 1password

# Proton Pass - https://proton.me/support/set-up-proton-pass-linux
wget https://proton.me/download/pass/linux/ProtonPass.rpm
sudo dnf install ./ProtonPass.rpm

# VSCode
sudo dnf install code

# Zed
curl -f https://zed.dev/install.sh | sh

# Tailscale
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up

# PIA VPN
wget https://installers.privateinternetaccess.com/download/pia-linux-3.7.2-08420.run
./pia-linux-3.7.2-08420.run

# GitHub CLI
sudo dnf install dnf5-plugins
sudo dnf config-manager addrepo --from-repofile=https://cli.github.com/packages/rpm/gh-cli.repo
sudo dnf install gh --repo gh-cli

# Nerd Font
sudo dnf install cascadia-mono-nf-fonts

# Create a Konsole profile using the Nerd Font, and set it as the default
mkdir -p ~/.local/share/konsole
cat > ~/.local/share/konsole/Main.profile << 'EOF'
[Appearance]
Font=Cascadia Mono NF,11,-1,5,400,0,0,0,0,0,0,0,0,0,0,1

[General]
Name=Main
Parent=FALLBACK/
EOF

kwriteconfig6 --file konsolerc --group "Desktop Entry" --key DefaultProfile Main.profile

# MPV
sudo dnf install mpv

# Terra Packages - https://terrapkg.com/
sudo dnf install --nogpgcheck --repofrompath 'terra,https://repos.fyralabs.com/terra$releasever' terra-release
```

## OpenSSH Server

This is optional, but useful for remote access through Tailscale SSH.

```
sudo systemctl start sshd.service
sudo systemctl enable sshd.service
```

## KDE Tweaks

Open System Settings

- Appearance > Breeze Dark
- Input & Output > Mouse & Touchpad > Trackpad 
	- Enable Invert scroll


Dolphin
- Configure > Startup
	- Show on startup > <home folder>
- Enable 'Details view mode' and set zoom to 16
