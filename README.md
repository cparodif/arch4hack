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

```console
pacman -S burpsuite
pacman -S evil-winrm  responder  whatweb wfuzz gobuster

paru -S asciidoctor
paru -S ldoc
paru -S xmlto
paru -S cmake
sudo pacman -Syu cmake
```

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
lista de categorías
al filtrar por el primer elemento:
```console
pacman -Sgg | grep blackarch | awk '{print $1}' | sort -u | less 
```
Para instalar todas las herramientas que involucren pentesting sobre bluetooth podríamos hacer:


pacman -S blackarch-bluetooth 

Enter

Ctrl+C no instalar

```console

cd /home/solr4c/Desktop/solr4c/repos

git clone https://github.com/cparodif/arch4hack.git 

git clone https://github.com/rxyhn/dotfiles.git

git clone https://aur.archlinux.org/awesome-git.git 

git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k 

git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf

git clone https://github.com/NvChad/NvChad.git  ~/.config/nvim --depth 1
```

### Si usamos VirtualBox

Buscamos y descargamos la iso: virtualbox-guest-iso

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



###  cargamos una interfaz gráfica en nuestro sistema

```console
systemctl start gdm.service 
```
Solicita hacer login:


Arranca la interfaz gráfica y podemos hacer login

La proporción de pantalla es incorrecta

Nos logueamos y estamos con gnome
Terminal no abre

Ctrl + Alt + F3

Nos abre otra ventana linux

Nos logueamos con nuestro usuario

Nos hacemos root con
```console
sudo su
systemctl enable gdm.service 

sudo systemctl start NetworkManager.service
```
[sudo] password for solr4c:
```console
systemctl enable NetworkManager
ping -c 1 google.com
```
1 packets transmitted, 1 received



### Cargamos el sistema operativo con las proporciones correctas

Nos logueamos y accedemos al entorno

En type to search buscamos kitty
 
Abrimos la kitty (terminal linux)

En kitty 
```console
whoami
```
solr4c

El guión no funcion
 
Click en botón Activities

En type to search buscamos Keyboard

Abrimos Keyboard Change 

Settings - Keyboard

En input source click en +

Add an Input Source

Spanish [Add]

y vamos a borrar el English (tres puntos y remove)

Volvemos a kitty probamos el guión y cerramos kitty con 

```console
exit
```
Máquina -> Tomar instantanea ->
Nombre de instantánea= Interfaz gráfica gdm y terminal kitty


Click en botón Activities

En type to search buscamos kitty 

Abrimos la kitty y con 
```console
sudo su
```
nos ponemos como root


### Vemos el navegador Firefox

Click en botón Activities

En type to search buscamos firefox 

Abrimos firefox 

### Instalamos AwesomeWM junto con PICOM y otros requerimientos

navegar a https://github.com/rxyhn/dotfiles
Leemos readme.md y vamos a setup

Click en botón Activities

En type to search buscamos kitty 

Abrimos la kitty

```console
paru -S awesome-git 
```
PKGBUILDs

Podemos copiar y pegar en kitty con: ctrl+Mayus+C y ctrl+Mayus+V

En otros sistemas, normalmente para copiar y pegar se utiliza: ctrl+C y ctrl+V


Versión 27/3/2022:
```console
paru -S awesome-git picom-git alacritty rofi todo-bin acpi acpid \     
wireless_tools jq inotify-tools polkit-gnome xdotool xclip maim \

brightnessctl alsa-utils alsa-tools pulseaudio lm_sensors \ 

mpd mpc mpdris2 ncmpcpp playerctl --needed 
```

Como obtenemos errores en instalación de cmake, brightnessctl, y mpd, para evitarlos podemos utilizar pacman -Syu cmake , paru -S brightnessctl --need y paru -S mpd --need como vemos a continuación :
```
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

```
paru -S brightnessctl --need 
```

```
paru -S mpd --need 
```
Queda instalado sin ningún error

En la kitty

ctrl + Shift

For automatically launching mpd on login

```
systemctl --user enable mpd.service
systemctl --user start mpd.service
```
For charger plug/unplug events (if you have a battery)

```
sudo systemctl enable acpid.service
sudo systemctl start acpid.service
```

###  Procedemos con la instalación de algunas fuentes necesarias


cd /usr/share/fonts
ls
sudo su
ls
wget http://fontlot.com/downfile/5baeb08d06494fc84dbe36210f6f0ad5.105610 
```
Enter
```
file 5baeb08d06494fc84dbe36210f6f0ad5.105610 
```
Zip ar
```
zip
```
está instalado
```
unzip
```
está instalado
```
tar
```
está instalado 
```
7z
```
no está instalado

[root@minihost fonts]#

Instalamos 7z
```
pacman -S p7zip
```
:: Proceed with installation? [Y/n]  

Enter

```
ls
7z l 5baeb08d06494fc84dbe36210f6f0ad5.105610 
```
vemos varias fuentes .ttf

```
mv 5baeb08d06494fc84dbe36210f6f0ad5.105610 comprimido.zip
unzip comprimido.zip  
ls
rm comprimido.zip
find .
```
Encontramos los recursos
```
find . | grep "\.ttf$" 
pwd 
/usr/share/fonts 
```
[root@minihost fonts]#

```
find . | grep "\.ttf$" | while read line; do cp $line .; done
rm -r iosevka-2.2.1/
rm -r iosevka-slab-2.2.1/
ls
```
En firefox descargar icomoon.zip desde el siguiente enlace:

[https://dropbox.com/s/hrkub2yo9iapljz/icomoon.zip?dl=0](https://dropbox.com/s/hrkub2yo9iapljz/icomoon.zip?dl=0)
```
mv /home/solr4c/Downloads/icomoon.zip .
unzip icomoon.zip
mv icomoon/*.ttf .
rm -rf icomoon
ls 
exit
paru -S nerd-fonts-jetbrains-mono ttf-font-awesome ttf-font-awesome-4 ttf-material-design-icons 
```
[sudo] password for solr4c:

:: Proceed with installation? [Y/n] 
 
Enter

:: Proceed with installation? [Y/n]  

Enter

:: Proceed with installation? [Y/n]  

Enter

### 44:00 - Cargamos la configuración de nuestro entorno en Awesome


Click en botón Activities

En type to search buscamos firefox 

Abrimos firefox y vamos a la dirección:

https://github.com/rxyhn/dotfiles

En kitty

[solr4c@minihost fonts]$
```
cd 
```
[solr4c@minihost ~]$
```
pwd 
```
/home/solr4c
```
cd Desktop/solr4c/repos 
git clone https://github.com/rxyhn/dotfiles.git
cd dotfiles
mkdir /home/solr4c/.local/bin/
cp -r config/* ~/.config/
cp -r bin/* ~/.local/bin/
cp -r misc/. ~/
sudo reboot now 
```

### 45:12 - Migramos a Awesome

Arrancamos ArchLinux, hacemos click en usuario, y antes de loguearnos, en la rueda dentada abajo a la derecha podemos activar awesome:

Seleccionaremos otro escritorio en la rueda dentada abajo a la derecha, donde haciendo click nos muestra cuatro escritorios:

awesome

GNOME

GNOME Classic

GNOME on Xorg

Seleccionamos awesome y nos logueamos.

Como no funciona bien el escritorio, continuamos haciendo modificaciones.

### 45:44 - Arreglamos un pequeño problema en la visualización migrando a un commit


Abrimos ventana de texto con
```
Alt + F3
```
Nos logueamos

```
cd Desktop/solr4c/repos
ls
cd dotfiles
git log
```
Vamos a volver al commit fix: ui and widgets

https://github.com/rxyhn/dotfiles/tree/c1e2eef2baa91aebd37324891cb282666beae04f

```
xclip
git log | grep commit 
git log | grep commit | grep "^c1" | awk 'NR==3'  
git log | grep commit | grep "c1" | awk 'NR==3' 
git log | grep commit | grep "c1" | awk 'NR==3' | awk 'NF{print $NF}'
git log | grep commit | grep "c1" | awk 'NR==3' | awk 'NF{print $NF}' | xclip -sel clip  
```
error
```
git checkout $(git log | grep commit | grep "c1" | awk 'NR==3' | awk 'NF{print $NF}')
```
switching to 'c1e2...'.
```
ls 
history 
cp -r config/* ~/.config/
cp -r bin/* ~/.local/bin/
cp -r misc/ ~/
sudo reboot now 
```
Reiniciamos
```
sudo pacman -S --needed base-devel git
cd Desktop/solr4c/repos
git clone https://aur.archlinux.org/awesome-git.git 
cd awesome-git
makepkg -fsri
```
Obteniendo los paquetes...

ERROR: «pacman» ha fallado al instalar las dependencias que faltaban.

==> Dependencias que faltan:

  -> asciidoctor

  -> cmake

  -> ldoc

  -> xmlto

==> ERROR: No se pudieron resolver todas las dependencias.


no se pudo descargar cmake-3.23.0-1-x86_64.pkg.tar.zst

Total (1/4)            10,5 MiB  1250 KiB/s 00:09 [#######################] 100%

error: no se pudo obtener el archivo «cmake-3.23.0-1-x86_64.pkg.tar.zst

pacman -S cmake

no se pudo descargar cmake-3.23.0-1-x86_64.pkg.tar.zst


```
paru -S asciidoctor
paru -S ldoc
paru -S xmlto
paru -S cmake
sudo pacman -Syu cmake
makepkg -fsri
```
ahora que hemos utilizamos 
```
sudo pacman -Syu cmake
```
si se instaló awesome-git 



### 47:55 - Tenemos el entorno de AwesomeWM funcionando correctamente

Nos logueamos

Funciona el volumen

Funciona el área de notificaciones

Abrimos otra ventana con
```
Ctrl + Alt + F3
```
Nos logueamos como solr4c

### 48:35 - Cambiamos el tipo de terminal que se lanza por defecto
```
Windows + Enter
```
no abre consola de terminal
```
cd ~/.config/awesome
ls
nano rc.lua
```
cambiamos
```
terminal = "alacritty"
por
terminal = "kitty"
```

Guardamos Ctrl+O y Salimos Ctrl+X
```
Ctrl + Windows + R 
```
Recargamos la configuración de los terminales y
```
Windows + Enter
```
abre consola de terminal kitty 

Vamos a la ventana 3
```
Ctrl + Alt + F3
```
Instalamos el lenguaje de scripts zsh 
```
sudo pacman -S zsh 
```
[sudo] password for solr4c:

:: Proceed with installation? [Y/n]  

Enter

Cambiamos termina kitty
```
echo $SHELL
``` 
/bin/bash
```
sudo su  
```
[sudo] password for solr4c:

Nos ponemos como root

Configuramos zsh como lenguaje shell por defecto para el usuairo solr4c
```
usermod --shell /usr/bin/zsh solr4c 
```

### 49:49 - Configuramos una ZSH por defecto como tipo de Shell para el usuario no privilegiado

Cerramos kitty y volvemos a abrir
```
Windows + Enter
```
abre consola de terminal kitty

Y ahora ya está zsh por defecto
```
echo $SHELL
``` 
/bin/zsh 

### 50:22 - Definimos la distribución del teclado con entorno gráfico

En kitty
```
whoami
```
solr4c
```
sudo su
```
[sudo] password:

Nos ponemos como root
```
localectl set-x11-keymap es  
```

cerramos kitty tecleando:
```
Crtl + Win + Q 
```

Vamos al panel de bloqueo

Nos indentificamos
```
Win+Enter
```
probamos que funciona el guión la coma y otros
```
cat ~/.zshrc 
```

### 51:01 - Configuramos el archivo zshrc de nuestro usuario

Click en botón Activities

En type to search buscamos firefox 

Abrimos firefox 

Win + 1

Win + 2

Win + 3 

Nos movemos a la segunda pestaña con 
```
Win + 2
```
En firefox abrimos el sitio:

https://solr4c.github.io  

En la segunda página vamos a 

Archivos de configuración de Bspwm

Buscamos zshrc 

instalar ese archivo de configuración de zshrc 

```
vim ~/.zshrc 
```
10000 dd

borra todas las líneas

pegamos la nueva configuración

Guardar y cerrar

Cerrar kitty tecleando
```
Win + Q
```

### 52:54 - Instalamos una serie de plugins para la ZSH
```
paru -S zsh-syntax-highlighting zsh-autosuggestions 
```
passwd:
```
sudo pacman -S locate 
```
:: Proceed with installation? [Y/n]  

Enter
```
sudo updatedb 
```
sincronizamos todos los ficheros para que locate funcione correctamente
```
locate zsh-syntax-highlighting.zsh 
```
/usr/share/zsh/plugins/zsh-sintax-highlighting/zsh-sintax-highlighting.zsh
```
locate zsh-autosuggestions.zsh 
```
/usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
```
nano ~/.zshrc 
```
filtramos por source (tres veces) y llegamos a 

* Plugins

cambiamos
```
source /usr/share/zsh/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
por 
source /usr/share/zsh/plugins/zsh-sintax-highlighting/zsh-sintax-highlighting.zsh
```
y cambiamos 
```
source /usr/share/zsh/zsh-autosuggestions/zsh-autosuggestions.zsh
por 
source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
```
Salimos de kitty y abrimos una nueva terminal
```
whoami
```
y ahora hacemos 
```
who 
```
está detectando la palabra mediante autosugestion
```
cat /etc/passw | grep "osidnf" NO-ENTER 
```
tambien nos detecta los colorines
```
whoami ESC ESC 
```
se cambia a 
```
sudo su whoami
```
buscar en google el texto: zsh sudo github 

y vamos a 

https://github.com/ohmyzsh/ohmyzsh/blob/master/plugins/sudo/sudo.plugin.zsh

vemos el archivo en raw y lo copiamos
```
cd /usr/share 
sudo su 
```
passwd: 
```
pwd
```
/usr/share 

[roo@minihost share]# 
```
mkdir zsh-sudo
chown solr4c:solr4c zsh-sudo/
cd !$ 
```
cd zsh-sudo

[roo@minihost zsh-sudo]#
```
exit
```
solr4c@minihost /usr/share % 
```
cd zsh-sudo 
```
solr4c@minihost /usr/share/zsh-sudo %
```
wget https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/plugins/sudo/sudo.plugin.zsh 
```
Cierro con Win + Q 

y abro con Win + Enter

tenemos una nueva terminal kitty
```
whoami 
```
Esc Esc

y aparece sudo porque ya funciona
```
sudo whoami
```

###  56:42 - Instalamos lsd y bat

```
pacman -S lsd bat
```
 Esc Esc
```
sudo pacman -S lsd bat 
```
passwd:

:: Proceed with installation? [Y/n]  

Enter

Ahora las cosas se ven mejor presentadas
```
cat /etc/passwd
/bin/cat /etc/passwd 
ls -l
sudo su
cat /etc/passwd
```
Una cosa es la zshrc de root y otra cosa es la zshrc de solr4c

Con un link simbolico vamos a lograr que la zsh de root sea la misma que la de solr4c 
```
paru -S scrub
```
:: Proceed with installation? [Y/n]  

Enter

Sirve para borrar archivos con mayor seguridad. Más información:

https://github.com/chaos/scrub/

```
wich rmk
cd Desktop
touch file.txt
echo "hola" > file.txt
rmk file.txt
```
scrub .... lo borra y es mejor que un rm

### 58:43 - Instalamos y cargamos la fuente HackNerdFonts

En firefox
 
Buscamos en google

HackNerdFonts

y vamos a 

https://www.nerdfonts.com/

Es la fuente a configurar en la kitty

https://www.nerdfonts.com/font-downloads

Descargamos 

https://github.com/ryanoasis/nerd-fonts/releases/download/v2.1.0/Hack.zip

Buscamos Hack Nerd Font

Cambiamos a terminal kitty
```
sudo su
cd /usr/share/fonts
mv /home/solr4c/Downloads/Hack.zip . 
mv /home/solr4c/Descargas/Hack.zip . 
unzip Hack.zip
```
se han extraido en el directorio /usr/share/fonts 
```
rm Hack.zip
```

### 59:54 - Cargamos un archivo de configuración para la Kitty
```
cd ~/.config/kitty/
```
<s>
```
wget https://raw.githubusercontent.com/rxyhn/bspdots/main/config/kitty/kitty.conf
```
*** hecho ya  ***
```
nano ~/.config/kitty/kitty.conf
```
(que no existe)

En firefox
 
Buscamos en google rxyhn

abrimos 

https://github.com/rxyhn

vamos a bspdots 

https://github.com/rxyhn/bspdots

vamos a kitty 

https://github.com/rxyhn/bspdots/tree/main/config/kitty

Abrimos 

https://github.com/rxyhn/bspdots/blob/main/config/kitty/kitty.conf

abrir en modo raw 

Copiamos el contenido de 

kitty.conf 

***fin hecho ya ***
</s>
este es le contenido de kitty.conf 
```

enable_audio_bell no

include color.ini

font_family      Cartograph CF Italic

font_size 11

disable_ligatures never

url_color #fff

url_style curly

shell zsh
```

Vamos al terminal kitty
 
El contenido de kitty.conf lo pegamos en 
```
nano ~/.config/kitty/kitty.conf
cd ~/.config/kitty 
```
Descargamos color.ini desde
``` 
wget https://github.com/rxyhn/bspdots/blob/main/config/kitty/color.ini 
ls 
```

### 1:00:28 - Definimos una serie de configuraciones para la Kitty

```
nano kitty.conf 
```
cambiamos

```
font_family Cartograph CF Italic
por 
font_family HackNerdFonts 
```
cambiamos 
```
font_size 11
por 
font_size 13
```
cambiamos
```
url_color #fff
por
url_color #61afef  
```
En terminal kitty
``` 
Con Ctrl + Shif + Enter  Abrimos otro nuevo terminal
Con Ctrl + Arriba (cambiar arriba)
y Con Ctrl + Abajo (cambiar abajo)
Ctrl + Shift + W cierra la ventana 
Ctrl + Shift + R resizear la ventana 
(Con s small hace más pequeña la ventana)
Resize this windows
Press one of the following keys:
W - Wider
N - Narrower
T - Taller
S - Shorter
R - Reset 
Press Esc to quit resize mode
Hold donw Ctrl to double step size
Con q paramos 

Con Crtl + Shif + L reorganiza las ventanas
```
continuamos modificando kitty.conf 
```
nano kitty.conf 
```
debajo de 

url_style curly 

añadimos
```
map ctrl+left neighboring_window left 
map ctrl+right neighboring_window right
map ctrl+up neighboring_window up
map ctrl+down neighboring_window down 
 
map f1 copy_to_buffer a 
map f2 paste_from_buffer a 

map f3 copy_to_buffer b 
map f4 paste_from_buffer b 
 
cursor_shape beam
cursor_beam_tickness 1.8
 
mouse_hide_wait 3.0 
detect_urls yes 

repaint_delay 10
input_delay 3
sync_to_monitor yes 

map ctrl+shif+z toggle_layout stack 
tap_bar_style powerline

inactive_tab_background #e06c75
active_tab_background #98c379
inactive_tab_foreground #000000 
tap_bar_margin_color black
```

Ctrl + Shif + T
```
map ctrl+shift+enter new_window_width_cwd
map ctrl+shift+t new_window_width_cwd
```
Cierro terminal y vuelvo a abrir nueva ventana

Ctrl + Shif + Enter

mantiene el directorio al abrir la nueva ventana
```  
background_opacity 0.95
```

Copiar/pegar
Crtl + Shift + C 
Crtl + Shift + V 

Crtl+Shift+Alt+T cambiamos en nombre del terminal kitty  
Ctrl+Shift+Izda Cambia de terminal
Crtl+Shift+Dcha
Crtl+Shift+punto
``` 
nano kitty.conf 
```
Añadir debajo de 

url_style curly 
```
map ctrl+left neighboring_window left 
map ctrl+right neighboring_window right
map ctrl+up neighboring_window up
map ctrl+down neighboring_window down 
 
map f1 copy_to_buffer a 
map f2 paste_from_buffer a 

map f3 copy_to_buffer b 
map f4 paste_from_buffer b 
 
cursor_shape beam
cursor_beam_tickness 1.8
 
mouse_hide_wait 3.0 
detect_urls yes 

repaint_delay 10
input_delay 3
sync_to_monitor yes 

map ctrl+shif+z toggle_layout stack 
tap_bar_style powerline

inactive_tab_background #e06c75
active_tab_background #98c379
inactive_tab_foreground #000000 
tap_bar_margin_color black

map ctrl+shift+enter new_window_width_cwd
map ctrl+shift+t new_window_width_cwd
  
background_opacity 0.95
```


### 1:08:42 - Retocamos el archivo de configuración de PICOM

```
cd ~/.config/awesome/theme 
ls
```
<s>se le olvidó</s>
```
rm picom.conf
```
lo descargamos
 ```
wget https://raw.githubusercontent.com/rxyhn/bspdots/main/config/picom/picom.conf
```
<s>fin se le olvidó</s>
```
nano ~/.config/awesome/theme/picom.conf 
```
filtramos por vsync

En General Settings, cambiamos 
```
vsync = true;
por 
vsync = false;
```

filtramos por use-damage
En General Settings cambiamos 
```
use-damage = true;
por 
use-damage = false;
```
comprobamos que funciona con
```
picom --config picom.conf 
```
da varios errores
```
nano picom.conf 
```
filtramos por refresh-rate
En General Settings. eliminamos
``` 
refresh-rate = 0;
```
guardamos y volvemos a abrir:
```
picom --config picom.conf 
```
da otros errores más  
```
nano picom.conf 
```
filtramos por blur

En Blurring, comentamos todo el  párrafo (12 líneas) 

Guardar y salir

Salimos del sistema, 
```
Ctrl+Win+Q 
```
Volvemos a entrar en el sistema en el awesome

nos logueamos 
```
Win+Enter
```
para abrir nuevo terminal kitty
```
cd ~/.config/awesome/ui/decorations 
```

### 1:10:08 - Quitamos la decoración que trae consigo la terminal
```
ls 
nano init.lua 
nano ~/.config/awesome/ui/decorations/init.lua 
```

comentar las dos últimas líneas:
```
require("ui.decorations.titlebar")
require("ui.decorations.music")
```
cambiamos por
```
-- require("ui.decorations.titlebar")
-- require("ui.decorations.music")
```
cerramos y abrimos una nueva ventana de terminal

Ctrl+Win+R para recargar y releer las configuraciones.

Win+Enter Win+Enter Win+Enter va abriendo tres terminales

### 1:11:09 - Cargamos una nueva configuración para PICOM

abrimos firefox desde el terminal kitty
```
firefox &> /dev/null &
```
volvemos a 

https://github.com/rxyhn/bspdots/blob/main/config/picom/

Abrimos pico.conf 
```
https://github.com/rxyhn/bspdots/blob/main/config/picom/picom.conf
```
pasamos a formato raw

Seleccionamos código y copiamos 

cambiamos al directorio 
```
cd ~/.config/awesome/theme
```
Abrimos

<s> saltar </s>
```
nano picom.conf 
```
mejor abrimos con vim 
```
vim picom.conf 
```
 borro su contenido
```
10000 dd 
```
salir

<s> fin saltar </s>
```
rm picom.conf
```
lo descargamos
``` 
wget https://raw.githubusercontent.com/rxyhn/bspdots/main/config/picom/picom.conf
```
El archivo descargado en ~/.config/awesome/theme lo abrimos con nano:
```
nano ~/.config/awesome/theme/picom.conf 
```

filtramos por VSync y cambiamos
```
vsync = true;
por 
vsync = false;
```
filtramos por menu-opacity y cambiamos
```
menu-opacity = 1.0
por 
opacity = 1.0
```
filtramos por blur, En BLUR comentamos todo el  párrafo (12 líneas o más) 

filtramos por use-damage, en General Settings cambiamos:
``` 
use-damage = true;
por 
use-damage = false;
```
filtramos por round-borders, en CORNERS cambiamos
``` 
round-borders = 1;
por 
round-borders = 20;
```
filtramos por corner-radius, en CORNERS cambiamos
``` 
corner-radius = 0;
por 
corner-radius = 20;
```
guardamos
```
picom --config picom.conf 
```
Fatal error

filtramos por refresh-rate, en General Settings comentamos
```  
refresh-rate = 0;
# refresh-rate = 0;
```
guardamos
```
picom --config picom.conf 
```
Ctrl+Win+R 
```
pkill picom
picom --config picom.conf 
```
Win+Shift+S 

Win+ clicDerecho podemos resizear la ventana

Win+clicIzquierdo podemos mover la ventana

Win+Ctrl+Q

Pantalla de bloqueo y nos logueamos

### 1:12:40 - Configuramos los bordeados de las ventanas
```
nano ~/.config/awesome/theme/picom.conf 
```

filtramos por glx, en GENERAL cambiamos
```   
backend = 'glx';
por
backend = 'xrender';
```

### 1:15:32 - Cargamos un nuevo fondo de pantalla
```
cd ~/Desktop/solr4c
ls 
mkdir images/
ls
```
arch.jpg es el archivo configurado para fondo de pantalla
```
cd ~/Desktop/solr4c/images
wget https://images.wallpapersden.com/image/download/anonymous-hacker-working_bGllZ2mUmZqaraWkpJRmZ21lrWxnZQ.jpg
```
lo podemos abrir con  
```
kitty +kitten icat arch.jpg
```
kitty +kitten icat es para visualizar imagenes por pantalla 

tambien puede abrir con gif

para configurar el fondo
```
sudo pacman -S feh
```
pass:

:: Proceed with installation? [Y/n]  

Enter
```
feh --bg-fill arch.jpg
```

queda la imagen en el fondo 

Tecleando Win+Enter abre el terminal y aplica la transparencia

Tecleando Ctrl+Win+R se aplica la configuración correcta 

### 1:16:47 - Configuramos el fondo de pantalla definiendo la carga en rc.lua

```
nano ~/.config/awesome/rc.lua
```

al final del todo añadimos
```
-- Wallpaper
local wallpaper_cmd="feh --bg-fill /home/solr4c/Desktop/solr4c/images/arch.jpg"
os.execute(wallpaper_cmd)
```
guardar 
 
Ctrl+Win+R 

Vemos que funciona

Ctrl+Win+Q  Panel de bloqueo y nos logueamos

### 1:17:52 - Instalamos y configuramos la Powerlevel10k
```
cd ~/Desktop/solr4c/repos   
```

abrimos firefox desde el terminal kitty
```
firefox &> /dev/null &
```
buscamos en google: powerlevel10k y abrimos:
```
https://github.com/romkatv/powerlevel10k
```
En readme bajamos
  
Manual 

git clone ...

Copiamos los dos comandos 
```
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k 

echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc

zsh 
```

entramos en modo configuración de zsh 

Pregunta:
~~~
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
~~~
Ya está casi configurado

```
nano /home/solr4c/.p10k.zsh 
```
o bien: nano ~/.p10k.zsh 

o bien: vim ~/.p10k.zsh 

buscar POWERLEVEL9K_LEFT_PROMPT_ELEMENTS y en 

typeset -g POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(

añadir debajo de vcs
``` 
vcs
command_execution_time
context
# prompt_char 
)
```
buscar POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS y en

en typeset -g POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(
comentado todo para que no salga nada a la derecha
```
status 
por
#status
etc ...
```

guardar 

hacer lo mismo para usuario root 
```
sudo su
```

un link simbolico para que todo lo que hay 

[root@minihost solr4c]
```
ln -s -f /home/solr4c/.zshrc /root/.zshrc 
cd 
ls -la 
```
.zshrc -> /home/solr4c/.zshrc 

cerra y abrir nueva ventana

```
sudo su
usermod --shell /usr/bin/zsh root 
```
salimos

abrimos nuevo terminal
```
sudo su
```

Ahora NO debemos configurar las p10k

decimos que no

abrimos firefox desde el terminal kitty
```
firefox &> /dev/null &
sudo su 
cd 
rm -r /root/powerlevel10k 
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k 
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
nano /root/.zshrc 
```

eliminar si al final o por la mitan se ha duplicado varias veces  
~~~
source ~/powerlevel10k/powerlevel10k.zsh-theme
~~~
(hay una línea por el centro y varias al final, pero eliminamos todas esas líneas excepto la del final )

ahora estando en root haremos:
```
p10k configure
```
para voverla a reconfigurar

modificamos el archivo de configuración 
```
nano /root/.p10k.zsh 
```

```
nano ~/.p10k.zsh 
o 
vim ~/.p10k.zsh 
```

en typeset -g POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(
añadir debajo de vcs
```
vcs
command_execution_time
context
# prompt_char 
)
```
en typeset -g POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(
comentado todo para que no salga nada a la derecha
```
status 
por
#status
etc ...
``` 

guardar 

tambien queremos saber cuando estamos comoo root

estamos en nano

buscamos:

POWERLEVEL9K_CONTEXT_PREFIX

Y LA VAMOS A COMENTAR
```
 typeset -g POWERLEVEL9K_CONTEXT_PREFIX='%246Fwith '
  # typeset -g POWERLEVEL9K_CONTEXT_PREFIX='%246Fwith '
```
y como root vamos a poner un fuego en

POWERLEVEL9K_CONTEXT_ROOT_TEMPLATE=''

cerramos el terminal, abrimos terminal como solr4c

Abrimos firefox
```
firefox &> /dev/null &
```
y hacemos un 
```
disown 
```
para que no dependa de ningún terminal
e
n google vamos a HackNerdFont 

en la página vamos a

Cheat Sheet

buscamo la llama (está en la quinta o sexta fila) 
```
nf_dev_codeigniter , e780 ,  , '~^~@'
```

en Nos lo copiamos:

seleccionamos en Icon y lo copiamos y luego 

abrimos un terminal y nos ponemos como root
```
nano /root/.p10k.zsh
```
buscamos 
```
POWERLEVEL9K_CONTEXT_ROOT_TEMPLATE=' 
```
pegamos en 

Context format when running with privileges: bold user@hostname.

typeset -g POWERLEVEL9K_CONTEXT_ROOT_TEMPLATE='%B%n@%m'
```  
typeset -g POWERLEVEL9K_CONTEXT_ROOT_TEMPLATE='~^~@'
```
tecleamos en el terminal kitty:
```
zsh 
```
 

### 1:26:10 - Instalamos y configuramos la FZF

Es para buscar archivos rapidamente.

Buscar en google FZF Github
```
https://github.com/junegunn/fzf
```

Using git

Alternatively, you can "git clone" this repository to any directory and run install script.

- como usuario solr4c

```
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
```
y 
y 
y 


- como usuario root 

```
sudo su 
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
```
y 
y 
y 

Aabrir nueva terminal. Vamos a la raiz del sistema.
```
cat
``` 
y hacer Ctrl+T hace una bussqueda de ficheros en el sistema

en el simbolo > pones passwd
 
y hace una busqueda inteligente

```
whoami y Ctrl + T
who -q  y Ctrl + T 
```

### 1:27:48 - Instalamos y configuramos Neovim (NvChad)
```
sudo pacman -S neovim
```
y lo instalamos

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
git clone https://github.com/NvChad/NvChad ~/.config/nvim --depth 1
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
git clone https://github.com/NvChad/NvChad ~/.config/nvim --depth 1
nvim +'hi NormalFloat guibg=#1e222a' +PackerSync
```

### 1:30:27 - Configuramos imágenes personalizadas en la barra de tareas

- como usuario solr4c 
su solr4c
vamos a 
```
cd .config/awesome/theme/assets/icons
ls 
```
debemos cambiar el archivo awesome.png
``` 
kitty +kitten icat awesome.png  
rm awesome.png 
```

copiar otro logo con ese nombre de archivo

y se cambia el icono "a"


### 1:31:05 - Creamos nuestros propios shortcuts para abrir aplicaciones (rc.lua)

configuramos para abrif firefox con las teclas: Win+Shift+F abrir 
```
cd ~/.config/awesome/ 
nano rc.lua 
``` 

buscamos en Default Applications
```
browser = "firefox" 
```
y se deja así

```
ls
cd configuration 
o 
cd ~/.config/awesome/configuration

nano keys.lua 
```
o directamente podriamos hacer:

```
nano ~/.config/awesome/configuration/keys.lua 
```
filtramos por browser y cambiamos 
```
awful.key({modkey}, "w", function()
awful.spawn.with_shell(browser) 
end,
```  
por
``` 
awful.key({modkey, "Shift"}, "f", function()
awful.spawn.with_shell(browser) 
end, 
```
guardamos y salimos
```
Win+Ctrl+R 
``` 
Recargar la configuración, y haciendo
```
Win+Shift+f 
```
Se abre firefox





### 1:31:56 - Creamos otro shortcut como modo de ejemplo

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

I Aceppt 

y Delete

close 

next 

start here 

start Burp 

Tema oscuro

User options -> Display -> Theme = Dark , Font-size = 15 

Configurar shortcut 
```
cd ~/.config/awesome/ 
nano rc.lua 
```
En 

-- Default Applications
debajo de browser añadimos una variable por ejemplo 
```
burp_proxy = "burpsuite" 
```
Guardamos y salimos
```
cd ~/.config/awesome/configuration
nano keys.lua
o 
nvim keys.lua 
```
filtramos por browser y añadimos 
``` 
awful.key({modkey,"Shift"}, "b", function()
            awful.spawn.with_shell(burp_proxy)
        end,
        {description = "open burpsuite", group = "launcher"}),
```
o
```	
awful.key({modkey, "Shift"}, "b", function()
awful.spawn.with_shell(burp_proxy) 
end, 
{description = "open burpsuite", group = "launcher"}),
```

### 1:36:00 - Instalamos algunas herramientas de pentesting

```
sudo su 
pacman -S evil-winrm python-pip responder nmap whatweb wfuzz gobuster 
```
instalarlos
```
pacman -S metasploit
```
da error y probamos con 
``` 
sudo pacman -Syu metasploit
```
funciona  bien
```
msfconsole 
```
msf6 > 
```
search eternalblue 
exit 
nmap
whatweb 
smbserver.py smbFolder $(pwd) -smb2support 
```

#### 1:37:33 - Configuramos las teclas Inicio, Fin y Supr


Hay que arreglarlo en el zshrc
```
# nvim ~/.zshrc 
o 
# nano ~/.zshrc 
```
al final del archivo hay que añadir:
```
bindkey "^[[H" beginning-of-line 

bindkey "^[[F" end-of-line
bindkey "^[[3~" delete-char 
```

guardamos y salimos




### 1:39:09 - Optimizamos la movilidad por consola con la definición de nuevos atajos
```
nvim ~/.zshrc 
o 
nano ~/.zshrc 
```
al final del archivo hay que añadir:
```
bindkey "^[[1;3C" forwad-word
bindkey "^[[1;3D" backward-word 
```
Alt izda y Alt dcha saltan palabras en un párrafo del terminal


guardamos y salimos

Aplican para root y para solr4c porque están enlazados por un link simbolico 




### 1:40:13 - Instalamos mdcat para la visualización por consola de archivos Markdown

```
sudo pacman -S mdcat 
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


### 1:42:04 - Conclusiones finales y despedida

Probar el centro de notificaciones (campana)

click en Icono de inicio abre ventana de estado y hace resize de las otras ventanas

```
Win+Enter y Win+Enter 
```

```
Win+Espacio
```
modos de visualizacion de las ventanas de trabajo
```
neofetch 
```
I use arch by the way

ASÍ es el ENTORNO de un HACKER https://www.youtube.com/watch?v=fshLf6u8B-w&t=2393s por solr4c 

[![ASÍ es el ENTORNO de un HACKER](http://img.youtube.com/vi/fshLf6u8B-w/0.jpg)](http://www.youtube.com/watch?v=fshLf6u8B-w "ASÍ es el ENTORNO de un HACKER")

Referencias markdown en https://www.netmentor.es/Entrada/lenguaje-markdown 

Etiquetas de código bash https://github.com/highlightjs/highlight.js/blob/main/SUPPORTED_LANGUAGES.md


Ayuda con la configuración en VirtualBox:
- Pasando de Noob a Pro de Linux en 20 Minutos https://www.youtube.com/watch?v=VoJOOx2WLy0 por Antonio Sarosi
- Desarrollando mi Escritorio "Pro" en Arch Linux https://www.youtube.com/watch?v=tJtVDUSyvCY por Antonio Sarosi

Si efi, ver:

https://bbs.archlinux.org/viewtopic.php?id=252051

https://wiki.archlinux.org/title/EFI_system_partition

https://wiki.archlinux.org/title/GRUB

<s>si efi 
 ```
pacstrap /mnt efibootmgr
```

si efi --------------
 ```
mkinitcpio -p linux
```
```
grub-install /dev/sda
grub-install --target=x86_64-efi /dev/sda
grub-install --efi-directory=/boot/efi --target=x86_64-efi /dev/sda
```
 
fin si efi --------
 </s>
Más información en solr4c discord


