Pacman puede actualizar todos los paquetes del sistema con una sola orden. Esto proceso puede durar bastante dependiendo de cuánto tiempo haya estado el sistema sin actualizar. La siguiente orden sincroniza las bases de datos de los repositorios y actualiza los paquetes del sistema (excluyendo los paquetes «locales» que no estén en los repositorios configurados):

sudo su

pacman -Syu
pacman -Sy
pacman -S sudo
pacman -S vim nano
pacman -S neofetch
pacman -S git
pacman -Sgg
pacman -S impacket
pacman -S xorg xorg-server
pacman -S gnome
pacman -S kitty
pacman -S gtkmm 
pacman -S virtualbox-guest-iso
pacman -S firefox
pacman -Syu cmake 
pacman -S wget
pacman -S p7zip
pacman -S --needed base-devel git
pacman -S zsh 
pacman -S locate 
pacman -S lsd bat 
pacman -S feh
pacman -S neovim
pacman -S burpsuite
pacman -S evil-winrm python-pip responder nmap whatweb wfuzz gobuster
pacman -Syu metasploit
pacman -S mdcat 


cd /home/s4vitar/Desktop/s4vitar/repos 
git clone https://aur.archlinux.org/paru-bin.git
paru -S asciidoctor
paru -S ldoc
paru -S xmlto
paru -S cmake

git clone https://github.com/cparodif/arch4hack.git 
git clone https://github.com/rxyhn/dotfiles.git
git clone https://aur.archlinux.org/awesome-git.git 
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k 
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
git clone https://github.com/NvChad/NvChad ~/.config/nvim --depth 1

echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
~/.fzf/install
nvim +'hi NormalFloat guibg=#1e222a' +PackerSync


su s4vitar 

git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k 
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
git clone https://github.com/NvChad/NvChad ~/.config/nvim --depth 1



Configuraciones:

cd /home/s4vitar/Desktop/s4vitar/repos/awesome-git
makepkg -fsri

echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
~/.fzf/install
nvim +'hi NormalFloat guibg=#1e222a' +PackerSync

Copia ficheros configurados:
su s4vitar 
cd /home/s4vitar/Desktop/s4vitar/repos/arch4hack 
cp awesome/usuario/rc.lua ~/.config/awesome
cp awesome/usuario/configuration/keys.lua ~/.config/awesome/configuration/keys.lua
cp awesome/usuario/theme/assets/icons/awesome.png ~/.config/awesome/theme/assets/icons/awesome.png
cp etc/hosts etc/hosts
cp etc/locale.gen etc/locale.gen
cp etc/sudoers etc/sudoers
cp etc/vconsole.conf etc/vconsole.conf
cp kitty/usuario/kitty.conf ~/.config/kitty/kitty.conf
cp lua/usuario/init.lua ~/.config/awesome/ui/decorations/init.lua
cp p10k/root/.p10k.zsh /root/.p10k.zsh 
cp p10k/usuario/.p10k.zsh  /home/s4vitar/.p10k.zsh
cp picom/usuario/picom.conf ~/.config/awesome/theme/picom.conf
cp zsh/usuario/.zshrc /home/s4vitar/.zshrc
ln -s -f /home/s4vitar/.zshrc /root/.zshrc
