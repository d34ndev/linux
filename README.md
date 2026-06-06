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
sudo dnf install @development-tools htop btop wget curl xclip rust cargo openssl-devel libyaml-devel zlib-devel gmp-devel mysql-devel make automake gcc gcc-c++ kernel-devel ripgrep

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

mise use -g bun@latest
mise use -g ruby@latest
mise use -g node@lts

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

# Kopia
sudo rpm --import https://kopia.io/signing-key
cat <<EOF | sudo tee /etc/yum.repos.d/kopia.repo
[Kopia]
name=Kopia
baseurl=http://packages.kopia.io/rpm/stable/\$basearch/
gpgcheck=1
enabled=1
gpgkey=https://kopia.io/signing-key
EOF
sudo dnf install kopia kopia-ui


# Nextcloud
sudo dnf install nextcloud-client

# Others inc Discord from the RPM Fusion Repo
sudo dnf install thunderbird
sudo dnf install discord
flatpak install com.spotify.Client
flatpak install md.obsidian.Obsidian

# 1Password
sudo dnf install 1password

# VSCode
sudo dnf install code

# Tailscale
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up

# Nord VPN
sh <(wget -qO - https://downloads.nordcdn.com/apps/linux/install.sh) -p nordvpn-gui

# GitHub CLI
sudo dnf install dnf5-plugins
sudo dnf config-manager addrepo --from-repofile=https://cli.github.com/packages/rpm/gh-cli.repo
sudo dnf install gh --repo gh-cli

# Nerd Font
sudo dnf install cascadia-mono-nf-fonts
# Then reopen terminal, create a profile with the NerdFont

# MPV
sudo dnf install mpv
```

## PSR

Edit the grub file `sudo nano /etc/default/grub` and add `i915.enable_psr=0` to the end of `GRUB_CMDLINE_LINUX`. 
Then run `sudo grub2-mkconfig -o /boot/grub2/grub.cfg`

## SELinux

Might need to disable SELinux - https://docs.fedoraproject.org/en-US/quick-docs/selinux-changing-states-and-modes/#_disabling_selinux

## OpenSSH Server

This is optional, but useful for remote access through Tailscale SSH.

```
sudo systemctl start sshd.service
sudo systemctl enable sshd.service
```

## AI Tools

```
npm install -g @anthropic-ai/claude-code
npm install -g @google/gemini-cli
npm install -g @openai/codex
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
