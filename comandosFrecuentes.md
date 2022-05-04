### Videos
 
[![MÁS COMANDOS de LINUX para MANEJAR tu SERVER - PARTE 2! 18:07min 8/6/2021] (http://img.youtube.com/vi/LbR83223zdM/0.jpg)](http://www.youtube.com/watch?v=LbR83223zdM  "MÁS COMANDOS de LINUX para MANEJAR tu SERVER - PARTE 2! 18:07min 8/6/2021")

[![MÁS COMANDOS de LINUX para MANEJAR tu SERVER - PARTE 2! 18:07min 8/6/2021](http://img.youtube.com/vi/fshLf6u8B-w/0.jpg)](http://www.youtube.com/watch?v=LbR83223zdM "MÁS COMANDOS de LINUX para MANEJAR tu SERVER - PARTE 2! 18:07min 8/6/2021")
### Comandos frecuentes linux
```console
sudo pacman -Syu
sudo pacman -S tree
```
### MÁS COMANDOS de LINUX para MANEJAR tu SERVER - PARTE 2!

(https://www.youtube.com/watch?v=LbR83223zdM)

Estadisticas de red
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

cat /proc/cpuinfo
número de procesadores

con un procesador, el
load average (mide la carga del servidor) debe ser menor que uno. (Siempre debe ser menor que la cantidad de procesadores que tiene, si es mayor quiere decir que está sobrecargado)

cat /proc/loadavg

### Por hacer (ToDo)
SSH
Docker
Git
LINUX
https://www.youtube.com/watch?v=JdBO8zNmnc8&list=PLPC-uborOKn2AYrTz74s-rz_Vspv0wndl

