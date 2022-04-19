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
Si efi
ver
https://bbs.archlinux.org/viewtopic.php?id=252051

https://wiki.archlinux.org/title/EFI_system_partition

https://wiki.archlinux.org/title/GRUB

si efi
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
´´´
fin si efi --------
