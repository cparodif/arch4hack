# arch4hack. 
## Ayuda para configurar el entorno de trabajo propuesto por s4vitar en abril de 2022. 


ASÍ es el ENTORNO de un HACKER https://www.youtube.com/watch?v=fshLf6u8B-w&t=2393s por s4vitar 

[![ASÍ es el ENTORNO de un HACKER](http://img.youtube.com/vi/fshLf6u8B-w/0.jpg)](http://www.youtube.com/watch?v=fshLf6u8B-w "ASÍ es el ENTORNO de un HACKER")

Referencias markdown en https://www.netmentor.es/Entrada/lenguaje-markdown 

Etiquetas de código bash https://github.com/highlightjs/highlight.js/blob/main/SUPPORTED_LANGUAGES.md

### 00:00 - Introducción 

### 03:00 - Os hago un tour por el nuevo entorno

### 13:23 - Comenzamos a crear nuestro entorno desde cero

### 13:28 - Descargamos la ISO de Arch y la cargamos en VMWare o VirtualBox

VMWare Workstation Pro 15.5

1. https://archlinux.org/download/
2. http://ftp.rediris.es/mirror/archlinux/iso/2022.04.05/
3. http://ftp.rediris.es/mirror/archlinux/iso/2022.04.05/archlinux-2022.04.05-x86_64.iso

### 14:00 - Definimos las propiedades de nuestra máquina virtual
Nueva maquina virtual
- Typical(recomended)
- Selec=Installer disk image file (iso)
- Selec=Linux
- Selec=Debian 5 64bit
- Virtual machine name= Arch Linux Tutorial
- Max disk size= 20GB (80GB)
- Selec=Store virtual disk as a single file
- Customize hardware 
- Memoria ram= 4GB
- Procesadores=2
- Network adapter= Bridgeed: Connected directily to the physical network
- Selec= Replicate physical network connection state (el router asigna IP)
- Play (arrancar la máquina virtual)
- Abre modo instalación de Archlinux
- Selec=Arch Linux install medium (x86_64, BIOS)

Ayuda con la configuración en VirtualBox:
- Pasando de Noob a Pro de Linux en 20 Minutos https://www.youtube.com/watch?v=VoJOOx2WLy0 por Antonio Sarosi
- Desarrollando mi Escritorio "Pro" en Arch Linux https://www.youtube.com/watch?v=tJtVDUSyvCY por Antonio Sarosi

Nos abre la consola root@archiso donde seremos el usuario root

```console
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


### 15:48 - Definimos las particiones del sistema con cfdisk
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
- Restar 4.5GB al espacio libre 19.5GB=15GB
- New 
- Partition size: 15GB
- Primary
 -Añade= /dev/sda2

### 17:50 - Definimos la partición correspondiente al SWAP

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

### 18:25 - Procedemos a formatear las particiones que hemos definido

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

### 19:24 - Creamos un par de monturas para posteriormente ingresar a nuestro sistema

```console
mount /dev/sda2 /mnt
mkdir /mnt/boot
```

si no efi
```console
mount /dev/sda1 /mnt/boot
```
si efi
```console
mkdir /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi 
```

### 19:45 - Instalamos una serie de paquetes necesarios con pacstrap

```
pacstrap /mnt linux linux-firmware networkmanager grub wpa_supplicant base base-devel
```
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
 
### 20:23 - Creamos un archivo fstab

```
genfstab -U /mnt
```
crea un archivo con el siguiente contenido

/dev/sda2 UUID=ee....ee /   ext4  rw,relatime 0,1 

/dev/sda1 UUID=ee....ee /boot   vfat  rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=ascii,shortname=mixed,utf8,error=remount-ro 0,2 


Esto lo guardamos en el archivo /nmt/etc/fstab
```
genfstab -U /mnt > /mnt/etc/fstab

cat !$
```
cat /mnt/etc/fstab

### 20:58 - Ingresamos a nuestro sistema con arch-chroot

```
arch-chroot /mnt
```
[root@archiso /]#

Este va a ser nuestro sistema

### 21:12 - Creamos y definimos las contraseñas de nuestros usuarios

```
passwd 
```

New password: 
Retype new password: 

introducimos el password para el usuario root

Ahora nos creamos nuestro usuario:
```
ls /home/
```

en home no hay ningún directorio

Al crear el usuario también nos crea su directorio personal 

```
useradd -m s4vitar
ls /home/ -l
```

drwx------ 2 s4vitar s4vitar

```
passwd s4vitar
```
diamondjackson

### 21:51 - Asignamos a nuestro usuario al grupo wheel e instalamos el paquete de sudo

``` 
usermod -aG wheel s4vitar
groups s4vitar
```
wheel s4vitar
```
pacman -S sudo
pacman -S vim nano
```
teclear Y

### 22:47 - Asignamos un privilegio a nivel de sudoers para nuestro usuario

```
cp /etc/sudoers /home/solrac/Desktop/solrac/repos/arch4hack/etc/sudoers 

nano /etc/sudoers
```
Descomentamos la línea:

- ## Uncomment to allow members of group wheel to execute any command

%wheel ALL=(ALL:ALL) ALL

si hacemos 
```
sudo su 
```
pide la contraseñas

Si descomentamos la línea
- ## Same thing without a passord
%wheel ALL=(ALL:ALL) NOPASSWD: ALL
cuando hacemos 
```
sudo su 
```
No pide la contraseñas

Guardamos Ctrl+O y Salimos Ctrl+X


[s4vitar@archiso /]$
Cambiamos a usuario s4vitar
```
su s4vitar
id
```
uid=1000(s4vitar) gid=1000(s4vitar) groups=1000(svitar),998(wheel)
```
sudo su
```
[root@archiso /]#

```
exit
exit
```

### 23:27 - Configuramos las regiones

```
nano /etc/locale.gen
```

ctrl+w y filtrar por en_US y 
descomentamos

en_US.UTF-8 UTF-8

ctrl+w y filtrar por es_ES y descomentamos

es_ES.UTF-8 UTF-8

Guardamos Ctrl+O y Salimos Ctrl+X

```
locale-gen
loadkeys es
nano /etc/vconsole.conf
```
Añado

KEYMAP=es

Guardamos Ctrl+O y Salimos Ctrl+X

La proxima vez que se reinicie estará bien el teclado

### 24:20 - Instalamos el bootloader (GRUB)

```
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

### 24:52 - Definimos un par de archivos necesarios en el sistema

```
cat /etc/hostname
```
no existe

```
echo hack4u > /etc/hostname
```
mir4me
```
cat !$
nano /etc/hosts
```
127.0.0.1		localhost
::1				localhost
127.0.0.1		hack4u.localhost hack4u
mir4me.localhost mir4me
Guardamos Ctrl+O y Salimos Ctrl+X

```
ls
pacman -S neofetch
neofetch
exit
```


### 26:06 - Verificamos que el GRUB ha sido instalado correctamente

Reiniciar

```
reboot now
```
...
Selec=Arch Linux

Enter

login: savitar

passord: ...

Enter

[s4vitar@hack4u ~]$

### 26:40 - Habilitamos el servicio que nos permitirá tener conexión a internet

[s4vitar@hack4u ~]$ 

```
ping -c 1 google.es
```

failure

```
sudo systemctl star NetworkManager.service
```
[sudo] password for s4vitar:

```
sudo su
systemctl enable NetworkManager
ping -c 1 google.com
```
1 packets transmitted, 1 received

```
 systemctl start wpa_supplicant.service

systemctl enable wpa_supplicant.service
```

### 27:33 - Habilitamos AUR para tener acceso a una mayor cantidad de paquetes

Repositorio de la cdad arch-chroot
```
pacman -S git
```

(Yes/No) 

Y 

Enter

```
pwd
```
/home/s4vitar
```
exit
whoami
cd
pwd
```
/home/s4vitar

```
mkdir -p Desktop/s4vitar/repos
cd !$
```
cd Desktop/s4vitar/repos
```
ls
git clone https://aur.archlinux.org/paru-bin.git
ls
cd paru-bin/
ls
```
PKGBUILD

```
makepkg -si 
```
[sudo] password for s4vitar:

(Yes/No) Y

### 28:57 - Instalamos los repositorios de BlackArch en Arch Linux

[s4vitar@hack4u repos]$
```
mkdir blackarch
pwd
```
/home/s4vitar/Desktop/s4vitar/repos
```
ls
blackarch paru-bin
cd blackarch
ls
curl
curl -O https://blackarch.org/strap.sh
ls -l
```
veremos strap.sh (-rw-r--r-- sin permisos de ejecución)
```
chmod +x strap.sh
```
(hemos dado permisos de ejecución)
```
sudo su
./strap.sh 
pacman -Sy
```
:: Sinchronizing package 
- databases
- core
- extra
- community
- blackarch

### 30:46 - ¿Cómo podemos instalar herramientas de pentesting?

Para listar todas las herramientas contempladas por blackarch (a la izquierda son las categorías y a la derecha son las herramientas)
```
pacman -Sgg | grep blackarch 
```

pacman -S crackmapexec

:: Proceed with installation? [Y/n] n

no instalar

Para instalar toda la suite de impacket:
```
pacman -S impacket
:: Proceed with installation? [Y/n] Y 
```
Para listar las categorías: 
```
pacman -Sgg | grep blackarch 
pacman -Sgg | grep blackarch | awk '{print $1}'
```
lista de categorías
al filtrar por el primer elemento:
```
pacman -Sgg | grep blackarch | awk '{print $1}' | sort -u | less 
```
Para instalar todas las herramientas que involucren pentesting sobre bluetooth podríamos hacer:


pacman -S blackarch-bluetooth 

Enter

Ctrl+C no instalar

### 31:40 - Instalamos y cargamos una interfaz gráfica en nuestro sistema

```
pacman -S xorg xorg-server
```
Enter a selection (default=all): 
Enter

:: Proceed with installation? [Y/n]  

Enter
```
pacman -S gnome
```
Enter Enter Enter Enter Enter Enter

```
systemctl start gdm.service 
```

Arranca la interfaz gráfica y podemos hacer login

La proporción de pantalla es incorrecta

Nos logueamos y estamos con gnome
Terminal no abre

Ctrl + Alt + F3

Nos abre otra ventana linux

Nos logueamos con nuestro usuario

Nos hacemos root con
```
sudo su
systemctl enable gdm.service 
```

### 33:42 - Instalamos la Kitty (La configuraremos después)

```
pacman -S kitty
```
:: Proceed with installation? [Y/n]
  
Enter

```
reboot now
```
Se reinicia

Nos logueamos