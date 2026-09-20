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

# Docker
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker dean
sudo systemctl start docker
sudo systemctl enable docker

sudo wget -O /opt/docker-compose.yml https://raw.githubusercontent.com/deanpcmad/fedora/refs/heads/main/docker-compose.yml
sudo docker compose -f /opt/docker-compose.yml up -d

# Mise
sudo dnf copr enable jdxcode/mise
sudo dnf install -y mise

mise use -g ruby@latest

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

# Codex
curl -fsSL https://chatgpt.com/codex/install.sh | sh

# Claude
curl -fsSL https://claude.ai/install.sh | bash

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
