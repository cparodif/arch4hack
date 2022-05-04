## parte 1

### Comandos básicos (ubuntu)
```console
ls 
ls -alh
ls -alh images
```
Los alias se pueden ver en el archivo .bashrc 
```console
vim .bashrc
```
alias ll='ls- alF'
alias la='ls -A'
alias l='ls -CF'
```console
ll
la
l
```
copiar archivo (cp origen destino):
```console
cp readme.txt documents/ 
ll documents
cp readme.txt readme.txt.copia 
```
copiar directorio
```console
cp -a myMusic/ myMedia/
``` 
el argumento -a incluye el -r copia recursiva

Eliminar archivos
```console
ls documents
rm documents/readme.txt
ls documents
```
mover un archivo 
```console
mv readme.txt documents/
```
Eliminar  directorios
```console
rm -rf myMedia/myMusic/  
```
-r recursiva
-f forzar

mover  un directorio
```console
mv myMusic/ myMedia/
ll myMedia/ 
```
copiar inteligente directorio (backups)
```console
ls images
ls images2
rsync  -av images/ images2/
rm images2/image3.png
rsync  -av images/ images2/

```
-v muestra la progresion

Crear archivo
```console
touch archivo.txt
ll
> archivo2.txt
ll
```
Ver cuanto ocupa un directorio
```console
du -sh /var/lib/
du -s /var/lib/
du -sh /var/lib/*
```
-s suma el contenido
-h muestra en modo humano (megabytes)
```console
stat readme.txt.copia
```
Comprimir/Descomprimir zip
```console
zip -r images.zip images
unzip images.zip 
zipinfo images.zip
```
Ver arbol de directorios
```console
tree
```
find cal date bc 
```console
find . -mtime +5 (5 dias)
find . -iname 'archivo*' 
find . -iname 'Archivo*' 
find . -name 'Archivo*' 
find . -name 'Archivo*' -delete 
cal 
cal 11 2020
date
date -d "+7 days"
bc 
2+2
```
Ver procesos corriendo
```console
ps fax
```
Matar procesos corriendo

kill mata procesos por PID 
```console
kill 771
```
771 es el PID que aparece en ps fax

killall mata procesos por nombre 
```console
killall dhclient 
```
dhclient es el nombre de procesos que aparecen en ps fax

Visitar paginas web usando la línea de comandos con curl: requests HTTP via comando. 
```console
curl http://cadizpro.com
curl ifconfig.me 
```
ifconfig.me sitio web que me muestra mi IP 

Mostrar el contenido de un archivo cat
```console
cat readme.txt.copia
```
Filtrar texto con grep
```console
cat readme.txt
grep hola readme.txt
cat readme.txt | grep hola
grep hola readme.txt
cat readme.txt | grep hola | grep pelado
grep -v hola readme.txt
grep -l 
grep -l hola readme.txt
grep -l adios readme.txt
cp readme.txt myMedia/
cp readme.txt myMedia/readme2.txt
ll myMedia/
grep -r hola myMedia 
grep -r -l hola myMedia 
```
-v filtra lo que no está (negación)
-l archivos filtrados
-r es recursivo

Ver espacio disponible en el servidor
```console
df -h 
```
Procesos y uso de recursos
```console
top 
```
? ayuda
Mayus + P uso de procesador
Mayus + M uso de memoria

Shorcuts historial 
```console
Ctrl + r busca historial en terminal
history
history | grep hola 
Ctrl + r y escribo grep
Ctrl + r - busca atrás
flecha derecha
Ctrl + e (fin de linea)
Ctrl + a (inicio de línea)
Ctrl + <flecha> (mover de palabra en palabra) 
Ctrl + c Cancelar el comando
Ctrl + l limpiar terminal
Ctrl + w elimina última palabra
Ctrl + t intercambia últimos 2 caracteres
Esc T intercambia últimas 2 palabras

```
Autocompletar con la tecla tab
```console
ls espacio my tab autocompletar
```
Esperar 1 minuto
```console
sleep 1m
```
Ctrl + z pone proceso en pausa y bg lo pasa al background y fg lo vuelve a traer al foreground
```console
bg
fg 
```
para frenarlo Ctrl + c

Salir del servidor
```console
exit
```
o Ctrl + d 

## parte 2

### Comandos frecuentes linux
```console
sudo pacman -Syu
sudo pacman -S tree
```
### MÁS COMANDOS de LINUX para MANEJAR tu SERVER - PARTE 2!

netstat Estadisticas de red
sudo pacman -Syu net-tools
sudo paru -Syu
netstat -nr
netstat -i
netstat -natup
Ver conexiones
service nginx start
sudo pacman -Syu tcpdump
sudo tcpdump -i any -p tcp port 80
sudo tcpdump -i any host   192.168.1.1
sudo tcpdump -i any host   192.168.1.2  

nano
vim
i (o tecla insert)
Esc
:wq (salir guardando)
:q! (salir sin guardar)

updatedb
locate

screen -> Nueva sesion de terminal especial
sleep 1d
ctrl + a + d -> sale de screen sin matar la terminal
screen ls -> lista las screen
screen -r 2808 -> volver a screen 2808 es el identificador del screen
ps fax
tmux
https://youtu.be/LbR83223zdM
MAC LOVER
top -> administrador de tareas 
Mayus + P -> Orden por uso de CPU
Mayus + M -> Orden por uso de memoria

htop -> alternativa a top 
sudo pacman -S htop
htop

locate .log

less
less /var/log/pacman.log
less /var/log/vboxadd-install.log
Teclas: avpag repag etc
Buscar, teclear barra / y texto a buscar
con n nos lleva a la siguiente coincidencia
:q salir
y 
more

tail archivo

ver las 10 últimas líneas del archivo.
tail /var/log/pacman.log
tail -f /var/log/pacman.log
con -f queda esperando a ver si el archivo se actualiza

tail -n1 /var/log/pacman.log
muestra una línea

head archivo
ver las 10 primeras líneas del archivo.
head /var/log/pacman.log

SSH 
video ssh en 8 minutos

ssh root@ip 

touch archivo
toca un archivo si existe le cambia la fecha de modificación
si no, crea un archivo nuevo

df -h
free   (en bytes)
free -m (en megabytes)
free -g (en gigas)

nmap puertos abiertos en un servidor
o puertos abiertos en el router:
nmap 192.168.0.1
nmap IPdelRouter

cat /proc/cpuinfo
número de procesadores

con un procesador, el
load average (mide la carga del servidor) debe ser menor que uno. (Siempre debe ser menor que la cantidad de procesadores que tiene, si es mayor quiere decir que está sobrecargado)

cat /proc/loadavg


### Videos
 
1 [MAS DE 30 COMANDOS en LINUX para manejar tu SERVER! 19:20min 27/4/2021](https://www.youtube.com/watch?v=0BA4k3jweaE) 

[![MAS DE 30 COMANDOS en LINUX para manejar tu SERVER! 19:20min 27/4/2021](https://img.youtube.com/vi/0BA4k3jweaE/0.jpg)](https://www.youtube.com/watch?v=0BA4k3jweaE "MAS DE 30 COMANDOS en LINUX para manejar tu SERVER! 19:20min 27/4/2021-")

2 [MÁS COMANDOS de LINUX para MANEJAR tu SERVER - PARTE 2! 18:07min 8/6/2021](https://www.youtube.com/watch?v=LbR83223zdM) 

[![MÁS COMANDOS de LINUX para MANEJAR tu SERVER - PARTE 2! 18:07min 8/6/2021](https://img.youtube.com/vi/LbR83223zdM/0.jpg)](https://www.youtube.com/watch?v=LbR83223zdM "MÁS COMANDOS de LINUX para MANEJAR tu SERVER - PARTE 2! 18:07min 8/6/2021-")




### Por hacer (ToDo)
SSH
Docker
Git
LINUX
https://www.youtube.com/watch?v=JdBO8zNmnc8&list=PLPC-uborOKn2AYrTz74s-rz_Vspv0wndl

