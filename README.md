# arch4hack.  abril de 2022. 

1. https://archlinux.org/download/
2. http://ftp.rediris.es/mirror/archlinux/iso/2022.04.05/
3. http://ftp.rediris.es/mirror/archlinux/iso/2022.04.05/archlinux-2022.04.05-x86_64.iso el archivo descargado falla

4. https://mirror.rackspace.com/archlinux/iso/2022.04.05/archlinux-2022.04.05-x86_64.iso el archivo descargado falla


5. http://mirror.rackspace.com/archlinux/iso/2022.04.05/

Abrir VirtualBox  
Archivo -> preferencias
Idioma= Español
Pantalla 
  Tamaño máximo de pantalla de invitado:
  - Sugerencia
  Anchura= 1280
  Altura= 720
Red
  Nombre= NatNetwork
  Red CIDR= 10.0.2.0/24
  Pociones de red= Soporta DHCP: Si Check
Extensiones
  Oracle VM VirtualBox Extension Pack 
Proxy= Conexion directa a Internet

Máquina -> Nueva 
nombreNuevaMáquina = archlinux 
Propiedades de la máquina virtual (Virtual box)
Nueva maquina virtual

- NombreMaquinaVirtual= archlinux
- carpeta= D:\virtualbox
- Tipo= mLinux
- Versión= Arch Linux (64-bit)
-> Siguiente
- TamañoDeMemoriaRAM= 3072MB
-> Siguiente
Disco Duro
- Crear un disco duro virtual ahora
-> Crear
Tipo de archivo de disco duro
- VDI (VirtualBox Disk Image)
-> Next
Almacenamiento en unidad de disco duro física
- Tamaño fisico
-> Next
Ubicación del archivo y tamaño
- Seleccionar ruta y archivo .vdi (por defecto)
- Tamaño unidad disco duro virtual = 25GB  
->Crear

Tarda un tiempo y vemos nuestra máquina virtual que la vamos a configurar:
-> Configuración
General
- Avanzado
  Compartir portapapeles= Bidireccional
  Arrastrar y soltar= Bidireccional
Sistema
- Placa base 
  Chipset PIIX3
  No Habilitar EFI
- Procesador
  Procesadores= 2
- Pantalla
  Memoria de video= 128 MB
  Controlador gráfico= VMSVGA
Almacenamiento 
  Controlador: IDE
  - Vacío
  Controaldor: SATA
  - archlinux.vdi
Red
- Adaptador 1
  Conectado a: Adaptador puente (bridge)
  Nombre: Intel(R) ...
USB
- Habililtar controlador USB= Si check
  Seleccionar= Controlador USB 3.0 xHCI
    
 
- Iniciar (arrancar la máquina virtual)
Seleccionar disco de inicio:
  Clic en icono= Seleccionar un archivo de disco óptico virtual.
  En ventana Selector de disco óptico
  Clic en Añadir +:
  Seleccione un archivo de disco ópitco virtual
  Vamos a Este equipo > Descargas
  archlinux-2022.04.05-x86_64.iso 
  -> Abrir 
  Si falla el archivo de instalación, tendremos que apagar la máquina y buscar otra imagen.
VirtualBox administrador
  Maquina archlinux
  Configuracion
  Almacenamiento 
  Controlador Ide
  Unidad Optica
  Cargar archlinux-2022.04.05-x86_64.iso 
->  Iniciar
  
- Abre modo instalación de Archlinux
- Selec=Arch Linux install medium (x86_64, BIOS)
- Probin EDD ...
- Se abre un terminal 
root@archiso~ # 


### Nos abre la consola root@archiso donde seremos el usuario root

```
whoami 
````
El terminal nos responde: root

Configurar teclado en español
``` console
loadkeys es
``` 
Validar si tenemos internet
```console
ping -c 1 google.es
```
```console
echo $SHELL 
```
/usr/bin/zsh 

### Particiones con cfdisk
```console
cfdisk
```
- Select label type = dos
- New 
- Partition size: 512M
- Primary
- Bootable 
- Añade= /dev/sda1

- Ir a free space
- Restar 4.5GB al espacio libre 24.5GB=20GB
- New 
- Partition size: 20GB
- Primary
 -Añade= /dev/sda2

### Nuestra partición SWAP

- New 
- Partition size: 4.5GB
- Primary
- Añade= /dev/sda3
- Selec= Type
- Selec= 82 Linux swap/Solaris
- Selec=Write
- Are you sure you want to write the partition table to disk? = yes
- Selec= Quit
```console
 lsblk
```
Veremos sda, sda1, sda2, sda3

### Formatear nuestras particiones

formato a la partición para el arranque
```console 
mkfs.vfat -F 32 /dev/sda1
```
formato a la partición para el sistema
```console 
mkfs.ext4 /dev/sda2
```
formato a la partición para memoria de intercambio (swap)
```console 
mkswap /dev/sda3
```
```console 
swapon
```
(se activan los cambios del swap)

### Montamos sda2 en /mnt y sda1 en /mnt/boot

```console
mount /dev/sda2 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
```

### Instalamos algunos paquetes con pacstrap

```console
pacstrap /mnt linux linux-firmware networkmanager grub wpa_supplicant base base-devel
```


###  Creamos un archivo fstab guardando genfstab -U /mnt  en /nmt/etc/fstab

```console
genfstab -U /mnt > /mnt/etc/fstab
cat !$
```
cat /mnt/etc/fstab

### Ingresamos a nuestro sistema con arch-chroot

```console
arch-chroot /mnt
```

### Creamos y definimos las contraseñas de nuestros usuarios

```console
passwd 
```
New password: 
Retype new password: 

introducimos el password para el usuario root

Ahora nos creamos nuestro usuario:
```console
ls /home/
```
en home no hay ningún directorio

Al crear el usuario también nos crea su directorio personal 

```console
useradd -m solr4c
ls /home/ -l
```
drwx------ 2 solr4c solr4c
```
passwd solr4c
```
xxxxxxxx

### Asignamos nuestro usuario al grupo wheel e instalamos los paquetes de sudo, vim y nano

``` console
usermod -aG wheel solr4c
groups solr4c
```
wheel solr4c
```console
pacman -Syu sudo vim nano
```
teclear Y

### Asignamos un privilegio a nivel de sudoers para nuestro usuario

```console
nano /etc/sudoers
```
Descomentamos la línea:

- \## Uncomment to allow members of group wheel to execute any command
```console
%wheel ALL=(ALL:ALL) ALL
```
si hacemos 
```console
sudo su 
```
nos pedirá la contraseña.

Pero No nos pide contraseña si descomentamos la siguiente línea
```console
- \## Same thing without a passord
%wheel ALL=(ALL:ALL) NOPASSWD: ALL
```

Guardamos Ctrl+O y Salimos Ctrl+X



[solr4c@archiso /]$
Cambiamos a usuario solr4c
```console
su solr4c
id
```
uid=1000(solr4c) gid=1000(solr4c) groups=1000(svitar),998(wheel)
```console
sudo su
```
[root@archiso /]#

```console
exit
exit
```

### Configuramos las regiones

```console
nano /etc/locale.gen
```
ctrl+w y filtrar por en_US y descomentamos

en_US.UTF-8 UTF-8

ctrl+w y filtrar por es_ES y descomentamos

es_ES.UTF-8 UTF-8

Guardamos Ctrl+O y Salimos Ctrl+X

```console
locale-gen
loadkeys es
nano /etc/vconsole.conf
```
Añado
```console
KEYMAP=es
```

Guardamos Ctrl+O y Salimos Ctrl+X

La proxima vez que se reinicie estará bien el teclado

### Instalamos el bootloader (GRUB)

```console
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

### Definimos  /etc/hostname, /etc/hosts. Instalamos neofetch.

```console
cat /etc/hostname
```
no existe

```console
echo minihost > /etc/hostname
```
minihost
```console
cat !$
```
cat /etc/hostname 

minihost 

```console
nano /etc/hosts
```
```console
127.0.0.1		localhost

::1				localhost

127.0.0.1		minihost.localhost minihost
``` 
Guardamos Ctrl+O y Salimos Ctrl+X

```console
ls
pacman -S neofetch
neofetch
exit
```


### Verificamos que GRUB está instalado correctamente
estamos en [root@archiso /]#
exit
estamos en [root@archiso /]#
exit

una vez estemos en 
root@archiso ~ # 

Reiniciar

```console
halt 
```
Extraer 

VirtualBox administrador

  Maquina archlinux

  Configuracion

  Almacenamiento 

  Controlador Ide

  Unidad Optica

  IDE primario maestro 

  En disco azul

  Eliminar disco de la unidad virtual
  y desaparece

  archlinux-2022.04.05-x86_64.iso 

  y queda

  Vacio

  -> Aceptar

  -> Iniciar 

  Selec=Arch Linux

  Grub welcom to grub 

  ArchLinux

  minihost login:
  
  
login: solr4c

passord: ...

Enter

[solr4c@minihost ~]$

### Habilitamos el servicio para conectar a internet

[solr4c@minihost ~]$ 

```console
ping -c 1 google.es
```
failure

```console
sudo systemctl start  NetworkManager.service
```
[sudo] password for solr4c:

```console
sudo su
systemctl enable NetworkManager
ping -c 1 google.com
```
1 packets transmitted, 1 received

```console
systemctl start wpa_supplicant.service
systemctl enable wpa_supplicant.service
```

### Habilitamos AUR para tener acceso a una mayor cantidad de paquetes

Repositorio de la cdad arch-chroot

Se instalan los paquetes, seleccionando todas las opciones por defecto.

```console
pacman -S git 
pacman -S impacket
pacman -S xorg xorg-server
pacman -S gnome
pacman -Syu 
pacman -S gnome
pacman -S kitty
pacman -S gtkmm 
pacman -S virtualbox-guest-iso
pacman -S firefox
pacman -Syu cmake 
pacman -S wget
```
```console 
sudo pacman -S wget
```
:: Proceed with installation? [Y/n]  

Enter

```console
pacman -S p7zip
pacman -S --needed base-devel git
pacman -S zsh 
pacman -S locate 
pacman -S lsd bat 
pacman -S feh
pacman -S neovim
pacman -S python-pip nmap whatweb 
pacman -S mdcat
pacman -Syu metasploit
```
Reiniciar

minihost login:
 
login: solr4c

passord: ...

Enter

[solr4c@minihost ~]$

(Yes/No) Y 

Enter

```console
pwd
```
/home/solr4c
```console
exit
whoami
cd
pwd
```
/home/solr4c

```console
mkdir -p Desktop/solr4c/repos
cd !$
```
cd Desktop/solr4c/repos
```console
ls

git clone https://aur.archlinux.org/paru-bin.git
ls
cd paru-bin/
ls
```
PKGBUILD

```console
makepkg -si 
```
[sudo] password for solr4c:

(Yes/No) Y

### Instalamos los repositorios de BlackArch en Arch Linux

[solr4c@minihost repos]$
```console
cd ..
mkdir blackarch
pwd
```
/home/solr4c/Desktop/solr4c/repos
```console
ls
```
blackarch paru-bin
```console
cd blackarch
ls
curl
curl -O https://blackarch.org/strap.sh
ls -l
```
veremos strap.sh (-rw-r--r-- sin permisos de ejecución)
```console
chmod +x strap.sh
```
(hemos dado permisos de ejecución)
```console
sudo su
./strap.sh 
pacman -Sy
pacman -Syu
```
:: Sinchronizing package 
- databases
- core
- extra
- community
- blackarch

### Instalamos varios paquetes necesarios

```console
pacman -S evil-winrm  responder  whatweb wfuzz gobuster

paru -S asciidoctor
paru -S ldoc
paru -S xmlto
paru -S cmake
sudo pacman -Syu cmake

paru -S zsh-syntax-highlighting zsh-autosuggestions 
paru -S scrub
```


### instalar burpsuite

instalamos un proxy para interceptar peticiones = burpsuite 
```
sudo pacman -S burpsuite
```
passwd:

Enter (instala 1) jre-openjdk)

ya instalado lo 

abrimos en la segunda ventana
```
burpsuite
```
Term y conditions  
Deseleccionar Help improve Burp ...
I Aceppt 

y Delete

close 

next 

start here 

start Burp 

Tema oscuro

User options -> Display -> Theme = Dark , Font-size = 15 

Burp -> User options -> Save user options -> 

### ¿Cómo podemos instalar herramientas de pentesting?

Listar todas las herramientas contempladas por blackarch (a la izquierda son las categorías y a la derecha son las herramientas)
```console
pacman -Sgg | grep blackarch 
```

pacman -S crackmapexec

:: Proceed with installation? [Y/n] n

no instalar

Para instalar toda la suite de impacket:
```console
pacman -S impacket
```
:: Proceed with installation? [Y/n] Y 

Para listar las categorías: 
```console
pacman -Sgg | grep blackarch 
pacman -Sgg | grep blackarch | awk '{print $1}'
```
lista de categorías al filtrar por el primer elemento:
```console
pacman -Sgg | grep blackarch | awk '{print $1}' | sort -u | less 
```
Para instalar todas las herramientas que involucren pentesting sobre bluetooth podríamos hacer:

pacman -S blackarch-bluetooth 

Enter

Ctrl+C no instalar

### Clonamos varios repositorios de github
Estando en kitty, como usuario solr4c 
```console

cd /home/solr4c/Desktop/solr4c/repos
git clone https://github.com/rxyhn/dotfiles.git
git clone https://aur.archlinux.org/awesome-git.git 
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k 
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
git clone https://github.com/NvChad/NvChad.git  ~/.config/nvim --depth 1

```
estando en kitty, como usuario root:
```console
sudo su 
cd /root 
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
git clone https://github.com/NvChad/NvChad ~/.config/nvim --depth 1
```
### Buscamos, descargamos e instalamos la iso: virtualbox-guest-iso

Ver en youtube [Como Instalar Guest Additions En Archlinux, por Tuxer 76:](https://www.youtube.com/watch?v=Es_L34N6TP4) 
```console
sudo updatedb 
```
sincronizamos todos los ficheros para que locate funcione correctamente

```console
locate virtualbox-guest-iso
locate virtualbox 
```
Buscamos donde está la iso y la montamos en  
```console
/mnt/VBoxGuestAdditions
```
Con locate vitualbox encontramos la iso en:

```console
 /usr/lib/virtualbox/additions/VBoxGuestAdditions.iso
```

Ahora 

```console
cd /usr/lib/virtualbox/additions/
ls
sudo mkdir /mnt/VBoxGuestAdditions
sudo chmod +x  VBoxGuestAdditions.iso 
sudo chmod 777  VBoxGuestAdditions.iso 
sudo mount VBoxGuestAdditions.iso /mnt/VBoxGuestAdditions
```
ejecutamos  VBoxLinuxAdditions.run y reiniciamos 
```console
ls 
cd /mnt/VBoxGuestAdditions
sudo chmod +x  VBoxLinuxAdditions.run  
sudo su
su 
./VBoxLinuxAdditions.run
reboot now 
```

###  cargamos y activamos una interfaz gráfica y el servicio NetworkManager en nuestro sistema

```console
systemctl start gdm.service 
```
Solicita hacer login:

Arranca la interfaz gráfica y podemos hacer login. La proporción de pantalla es incorrecta. Nos logueamos y estamos con gnome. Terminal no abre

Ctrl + Alt + F3

Nos abre otra ventana linux. Nos logueamos con nuestro usuario. Nos hacemos root con
```console
sudo su
systemctl enable gdm.service 
sudo systemctl start NetworkManager.service
systemctl enable NetworkManager
ping -c 1 google.com
```
1 packets transmitted, 1 received


### Cargamos el sistema operativo con las proporciones correctas

Nos logueamos y accedemos al entorno. En type to search buscamos kitty. Abrimos la kitty (terminal linux). En kitty 
```console
whoami
```
solr4c

Comprobamos que la ventana de la máquina virtual se amplia correctamente.

### Activities -> Keyboard Change -> Settings - Keyboard -> imput source -> + -> Spanish
El guión no funciona. Click en botón Activities. En type to search buscamos Keyboard. Abrimos Keyboard Change. Settings - Keyboard. En input source click en +. Add an Input Source. Spanish [Add]. Y vamos a borrar el English (tres puntos y remove). Volvemos a kitty probamos el guión y cerramos kitty con 

```console
exit
```

### Tomamos una instantanea de la máquina virtual

Máquina -> Tomar instantanea ->
Nombre de instantánea= Interfaz gráfica gdm y terminal kitty


### Abrimos kitty
Click en botón Activities. En type to search buscamos kitty. Abrimos la kitty y con 
```console
sudo su
```
nos ponemos como root

### Vemos el navegador Firefox

Click en botón Activities. En type to search buscamos firefox. Abrimos firefox 

###  Procedemos con la instalación de algunas fuentes necesarias

```console
cd /usr/share/fonts
sudo wget http://fontlot.com/downfile/5baeb08d06494fc84dbe36210f6f0ad5.105610 
file 5baeb08d06494fc84dbe36210f6f0ad5.105610 
7z l 5baeb08d06494fc84dbe36210f6f0ad5.105610
sudo mv 5baeb08d06494fc84dbe36210f6f0ad5.105610 comprimido.zip
sudo unzip comprimido.zip  
sudo rm comprimido.zip
sudo  su
find .
find . | grep "\.ttf$" 
pwd 
/usr/share/fonts 
find . | grep "\.ttf$" | while read line; do sudo cp $line .; done
sudo rm -r iosevka-2.2.1/
sudo rm -r iosevka-slab-2.2.1/
sudo wget https://dropbox.com/s/hrkub2yo9iapljz/icomoon.zip
sudo unzip icomoon.zip
sudo mv icomoon/*.ttf .
sudo rm -rf icomoon
paru -S nerd-fonts-jetbrains-mono ttf-font-awesome ttf-font-awesome-4 ttf-material-design-icons 
```

### Instalamos y cargamos la fuente HackNerdFonts

```console
cd /usr/share/fonts
sudo wget https://github.com/ryanoasis/nerd-fonts/releases/download/v2.1.0/Hack.zip
sudo unzip Hack.zip
sudo rm Hack.zip
sudo su
cd /usr/share/fonts
mv /home/solr4c/Downloads/Hack.zip . 
mv /home/solr4c/Descargas/Hack.zip . 
unzip Hack.zip
rm Hack.zip
```



### AwesomeWM-1: Instalamos AwesomeWM junto con PICOM y otros requerimientos

navegar a https://github.com/rxyhn/dotfiles
Leemos su readme.md y vamos a setup. Click en botón Activities. En type to search buscamos kitty. Abrimos la kitty

```console
paru -S awesome-git 
```
PKGBUILDs
```
makepkg -fsri
```
Podemos copiar y pegar en kitty con: ctrl+Mayus+C y ctrl+Mayus+V

En otros sistemas, normalmente para copiar y pegar se utiliza: ctrl+C y ctrl+V

https://github.com/rxyhn/dotfiles/tree/c1e2eef2baa91aebd37324891cb282666beae04f

Versión 27/3/2022:
```console
sudo pacman -Syu 
paru -S awesome-git picom-git alacritty rofi todo-bin acpi acpid \     
wireless_tools jq inotify-tools polkit-gnome xdotool xclip maim \
brightnessctl alsa-utils alsa-tools pulseaudio lm_sensors \ 
mpd mpc mpdris2 ncmpcpp playerctl --needed
```

Como obtenemos errores en instalación de cmake, brightnessctl, y mpd, para evitarlos podemos utilizar pacman -Syu cmake , paru -S brightnessctl --need y paru -S mpd --need como vemos a continuación :
```console
sudo pacman -Syu cmake 
```

[sudo] passwd for solr4c:

tecleamos passord

:: Proceed with installation? [Y/n]  

Enter

Repository AUR:

1 picon-git Enter

Repository AUR:

1 mpdris2 Enter

 :: Proceed to review? [Y/n]  

Enter

Todo por default

pkgname=$(_pkgname)-git 

:: Proceed with installation? [Y/n]  
Enter

Total Installed Size: ... 125.06 MiB

:: Proceed with installation? [Y/n]  
Enter

```console
Si la instalación da problemas:
paru -S brightnessctl --needed 
paru -S mpd --needed 
paru -S wireless_tools --needed 
paru -S awesome-git picom-git alacritty rofi todo-bin acpi acpid \     
wireless_tools jq inotify-tools polkit-gnome xdotool xclip maim \
brightnessctl alsa-utils alsa-tools pulseaudio lm_sensors \ 
mpd mpc mpdris2 ncmpcpp playerctl --needed
```
Queda instalado awesome sin ningún error

En la kitty

ctrl + Shift

For automatically launching mpd on login. For charger plug/unplug events (if you have a battery)

```console
systemctl --user enable mpd.service
systemctl --user start mpd.service
sudo systemctl enable acpid.service
sudo systemctl start acpid.service
```


### AwesomeWM-2: Volvemos a cargamos la configuración de nuestro entorno en Awesome

Intento 2: Vamos a volver al commit fix: ui and widgets

https://github.com/rxyhn/dotfiles/tree/c1e2eef2baa91aebd37324891cb282666beae04f
```
cd /home/solr4c/Desktop/solr4c/repos/dotfiles
git checkout c1e2eef2b
o 
git checkout c1e2eef2baa91aebd37324891cb282666beae04f
mkdir /home/solr4c/.local/bin/
cp -r config/* ~/.config/
cp -r bin/* ~/.local/bin/
cp -r misc/. ~/
cd /home/solr4c/Desktop/solr4c/repos/awesome-git
makepkg -fsri
sudo reboot now 
```
### Descargamos el archivo de configuración kitty.conf y color.ini  desde rxyhn bspdots.
```console 
cd /home/solr4c/.config/kitty/

wget https://github.com/rxyhn/bspdots/blob/main/config/kitty/color.ini 

wget https://raw.githubusercontent.com/rxyhn/bspdots/main/config/kitty/kitty.conf
```
### Configuración zsh 
```console 
zsh 
```
entramos en modo configuración de zsh 

Preguntas:

- Ves un diamante
Yes 

- Ves un candado 
Yes 

- Ves el logo debian 
Yes 

- Ves los iconos separados sin que se crucen unos con otros, sin overlap
Yes 

Pront Style
2 Clasic

Character Set 
1 Unicode

Prompt Color
3 Dark 

Show current time?
1 no 

Prompt Separators
1 Angled 

Prompt Heads
1 Sharp

Prompt Tails
4 Slanted

Prompt Height
1 One line

Prompot Spacing
2 Sparce

Icons
2 Many icons 

Prompt Flow
2 Fluent

Enable Transient Prompt 
y Yes 

Instant Prompt Mode
1 Verbose

Ya está casi configurado

### Clonamos el repositorio arch4hack 
En la siguiente ubicación, disponemos de nuestros archivos de configuración que utilizaremos
```console 
cd /home/solr4c/Desktop/solr4c/repos

git clone https://github.com/cparodif/arch4hack.git 

cd /home/solr4c/Desktop/solr4c/repos/arch4hack 
```
Vamos a actualizamos los archivos de configuración de zsh, awesome, kitty y p10k.
 para ello haremos copia de seguridad de archivos de configuración que tenemos en estos momentos:
 
### Archivos de imagenes de icono awesome y del fondo de pantalla.

```console 
cp awesome/usuario/theme/assets/icons/awesome.png /home/solr4c/.config/awesome/theme/assets/icons/awesome.png

mkdir /home/solr4c/Desktop/solr4c/images/

cp awesome/fondo_1280x720.jpg /home/solr4c/Desktop/solr4c/images/arch.jpg

kitty +kitten icat /home/solr4c/Desktop/solr4c/images/arch.jpg
```

### Archivo de configuración de awesome 
```console 
cp /home/solr4c/.config/awesome/rc.lua awesome/usuario/original-rc.lua

cp awesome/usuario/rc.lua /home/solr4c/.config/awesome/rc.lua

nano /home/solr4c/.config/awesome/rc.lua

cp /home/solr4c/.config/awesome/theme/picom.conf picom/usuario/original-picom.conf

cp picom/usuario/picom.conf /home/solr4c/.config/awesome/theme/picom.conf 

nano /home/solr4c/.config/awesome/theme/picom.conf

cp /home/solr4c/.config/awesome/ui/decorations/init.lua lua/usuario/original-init.lua

cp lua/usuario/init.lua /home/solr4c/.config/awesome/ui/decorations/init.lua

nano /home/solr4c/.config/awesome/ui/decorations/init.lua

cp /home/solr4c/.config/awesome/configuration/keys.lua awesome/usuario/configuration/original-keys.lua

cp awesome/usuario/configuration/keys.lua /home/solr4c/.config/awesome/configuration/keys.lua 

nano /home/solr4c/.config/awesome/configuration/keys.lua 

```
### Archivo de configuración de zsh
```console 
cp /home/solr4c/.zshrc  zsh/usuario/.original-zshrc

cp zsh/usuario/.zshrc /home/solr4c/.zshrc

nano /home/solr4c/.zshrc 
```

### Archivo de configuración de kitty
```console 

cp /home/solr4c/.config/kitty/kitty.conf  kitty/usuario/original-kitty.conf

cp kitty/usuario/kitty.conf  /home/solr4c/.config/kitty/kitty.conf

nano /home/solr4c/.config/kitty/kitty.conf
```
### Archivo de configuración de p10k del usuario no privilegiado y del root 
```console 
sudo cp -r /home/solr4c/powerlevel10k/  /root/powerlevel10k/
#### /root/powerlevel10k/powerlevel10k.zsh-theme
cp /home/solr4c/powerlevel10k/internal/p10k.zsh  p10k/usuario/.original-p10k.zsh

cp p10k/usuario/.p10k.zsh  /home/solr4c/.p10k.zsh 

nano /home/solr4c/.p10k.zsh 

sudo cp p10k/root/.p10k.zsh  /root/.p10k.zsh

sudo nano /root/.p10k.zsh 

```

### Copia de seguridad de los archivos de configuración

```console   

mkdir /home/solr4c/Desktop/solr4c/repos/arch4hack/copiaSeguridadConfig 
cd /home/solr4c/Desktop/solr4c/repos/arch4hack/copiaSeguridadConfig 
mkdir -p awesome/usuario/theme/assets/icons
mkdir -p awesome/usuario/configuration/keys
mkdir -p picom/usuario
mkdir -p lua/usuario
mkdir -p zsh/usuario
mkdir -p kitty/usuario
mkdir -p p10k/usuario
mkdir -p p10k/root 

cp /home/solr4c/.config/awesome/theme/assets/icons/awesome.png awesome/usuario/theme/assets/icons/awesome.png 

cp  /home/solr4c/Desktop/solr4c/images/arch.jpg awesome/fondo_1280x720.jpg

cp /home/solr4c/.config/awesome/rc.lua awesome/usuario/rc.lua

cp /home/solr4c/.config/awesome/theme/picom.conf picom/usuario/picom.conf 

cp /home/solr4c/.config/awesome/ui/decorations/init.lua lua/usuario/init.lua

cp /home/solr4c/.config/awesome/configuration/keys.lua awesome/usuario/configuration/keys.lua

cp /home/solr4c/.zshrc zsh/usuario/.zshrc 

cp /home/solr4c/.config/kitty/kitty.conf kitty/usuario/kitty.conf 

cp /home/solr4c/.p10k.zsh p10k/usuario/.p10k.zsh  

sudo cp /root/.p10k.zsh p10k/root/.p10k.zsh 

```


### Volver a configurar p10k  

```console 
p10k configure
```

### Configuramos una ZSH por defecto como tipo de Shell para el usuario no privilegiado y para el root 

Configuramos zsh como lenguaje shell por defecto para el usuario solr4c y para root 
```console 
sudo usermod --shell /usr/bin/zsh solr4c 
sudo usermod --shell /usr/bin/zsh root 
echo $SHELL
```
un link simbolico para que todo lo que hay 

[root@minihost solr4c]
```console 
ls -lah /home/solr4c/.zshrc
sudo ls -lah /root/.zshrc 
sudo ln -s -f /home/solr4c/.zshrc /root/.zshrc 
sudo ls -lah /root/.zshrc 
```
/root/.zshrc -> /home/solr4c/.zshrc

### Definimos la distribución del teclado con entorno gráfico

En kitty

```
sudo localectl set-x11-keymap es  
``` 

### instalamos plugin zsh-sudo

```
cd /usr/share 
sudo mkdir /usr/share/zsh-sudo
sudo chown solr4c:solr4c zsh-sudo/
cd /usr/share/zsh-sudo
wget https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/plugins/sudo/sudo.plugin.zsh 
```

### Verificamos que la configuración de la carga del fondo de pantalla en /home/solr4c/.config/awesome/rc.lua

```
feh --bg-fill /home/solr4c/Desktop/solr4c/images/arch.jpg

nano /home/solr4c/.config/awesome/rc.lua
```
al final del todo añadimos
```
-- Wallpaper
local wallpaper_cmd="feh --bg-fill /home/solr4c/Desktop/solr4c/images/arch.jpg"
os.execute(wallpaper_cmd)
```

### AwesomeWM-3: Migramos a Awesome

Arrancamos ArchLinux, hacemos click en usuario, y antes de loguearnos, en la rueda dentada abajo a la derecha podemos activar awesome:

Seleccionaremos otro escritorio en la rueda dentada abajo a la derecha, donde haciendo click nos muestra cuatro escritorios:

awesome

GNOME

GNOME Classic

GNOME on Xorg

Seleccionamos awesome y nos logueamos. Funciona el volumen. Funciona el área de notificaciones. Abrimos otra ventana con
```
Ctrl + Alt + F3
```
Nos logueamos como solr4c


### Instalamos y configuramos la FZF

Es para buscar archivos rapidamente. Buscar en google FZF Github. En kitty, como usuario solr4c:

```console 
whoami 
~/.fzf/install

```
y, y, y, 
```console 
Update /home/solr4c/.bashrc:
  - [ -f ~/.fzf.bash ] && source ~/.fzf.bash
    + Added

Update /home/solr4c/.zshrc:
  - [ -f ~/.fzf.zsh ] && source ~/.fzf.zsh
    - Already exists: line #63 

Finished. Restart your shell or reload config file.
   source ~/.bashrc  # bash
   source ~/.zshrc   # zsh

Use uninstall script to remove fzf.

For more information, see: https://github.com/junegunn/fzf 
```
En kitty, como usuario root 

```console 
sudo su
cd /root  
~/.fzf/install
```
y, y, y 

archivos de configuración
~/.fzf.bash
~/.fzf.zsh 

Abrir nueva terminal. Vamos a la raiz del sistema.
```
cat
``` 
y hacer Ctrl+T hace una busqueda de ficheros en el sistema. en el simbolo > pones passwd, y hace una busqueda inteligente.

```
whoami y Ctrl + T
who -q  y Ctrl + T 
```
  
### Configuramos Neovim (NvChad)

abrimos firefox desde el terminal kitty
```
firefox &> /dev/null &
```
buscar nvchad github 
```
https://github.com/NvChad/NvChad
```
ir a wiki para configurarlo 
```
https://nvchad.github.io/
```
Vamos a V 1.0 para instalarlo:

- Como usuario solr4c:
```
nvim +'hi NormalFloat guibg=#1e222a' +PackerSync
```
se instala lo que necesitamos
```
espacio
```
y salimos con 
```
:q 
```
o :wq

Prueba

abrir archivo 
```
nvim test.py
```
poner

:NvimTreeToggle 

Aparece un árbol de archivos a la izquierda

lo mismo para instalarlo:

- Como usuario root:
```
sudo su
nvim +'hi NormalFloat guibg=#1e222a' +PackerSync
```

 

### Instalamos algunas herramientas de pentesting

``` 
sudo pacman -Syu metasploit

msfconsole 
```
msf6 > 
```
search eternalblue 
exit 
nmap
whatweb 
sudo smbserver.py smbFolder $(pwd) -smb2support 
```

### Instalamos mdcat para la visualización por consola de archivos Markdown

```
nvim test.md
```
Esto es una **prueba**:


python 
```
#!/usr/bin/python3
from pwn import *
print("Hola")  
```

Este script hace las siguiente cosas.

- Hola
- Probando
- Adios

Guardar y salir
```
mdcat test.md
```
se puede ver por consola

```
neofetch 
```
I use arch by the way


###  ********************************************************
###  *********** Fuentes de información *********************
###  ********************************************************

ASÍ es el ENTORNO de un HACKER https://www.youtube.com/watch?v=fshLf6u8B-w&t=2393s por solr4c 

[![ASÍ es el ENTORNO de un HACKER](http://img.youtube.com/vi/fshLf6u8B-w/0.jpg)](http://www.youtube.com/watch?v=fshLf6u8B-w "ASÍ es el ENTORNO de un HACKER")

Referencias markdown en https://www.netmentor.es/Entrada/lenguaje-markdown 

Etiquetas de código bash https://github.com/highlightjs/highlight.js/blob/main/SUPPORTED_LANGUAGES.md


Ayuda con la configuración en VirtualBox:
- Pasando de Noob a Pro de Linux en 20 Minutos https://www.youtube.com/watch?v=VoJOOx2WLy0 por Antonio Sarosi
- Desarrollando mi Escritorio "Pro" en Arch Linux https://www.youtube.com/watch?v=tJtVDUSyvCY por Antonio Sarosi

