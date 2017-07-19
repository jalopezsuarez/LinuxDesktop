## Thin Client (PC Intel x64 / Debian 8 / Xubuntu 16.04)

### Base System

Durante la instalacion el nombrel del equipo sera.
```
terminal
```

### Update System

Una vez instalado el sistema DebianLite lo primero que hay que hacer es actualizar el repositorio de programas.
```
sudo apt-get update
```

### Permisos Usuario

Se creara un usuario para uso de sistema.
```
administrator
```

Se instala el sistema de acceso root con usuarios normales.
```
apt-get install -y sudo
```

Se agrega al usuario `administrator` al grupo de administradores de sistema.X
```
sudo adduser administrator sudo
sudo usermod -a -G sudo administrator
sudo sh -c "echo 'administrator ALL=NOPASSWD: ALL' >> /etc/sudoers"
```

### Vim Editor (Fix Arrowkeys)

En el editor del box de consola se puede corregir algunas teclas especiales como cursores y borrar con el siguiente comando:

```
echo "set nocompatible" > ~/.vimrc
echo "set backspace=indent,eol,start" >> ~/.vimrc
```

### Debian Essentials Dependences

All dependences needed to build sucessufuly the applications from source.
```
sudo apt-get install -y build-essential
```

### Basic Building Tools
```
sudo apt-get install -y make automake cmake git subversion checkinstall unzip
```

### Dependencias Generales
```
sudo apt-get install -y intltool flex bison libdbus-glib-1-dev libglu1-mesa-dev freeglut3-dev mesa-common-dev wish libgd2-dev libgtkglext1-dev libgtk2.0-dev libgtk-3-dev libxml2-dev libsm-dev libmenu-cache-dev libfm-gtk4 libgl1-mesa-dri python3 python3-gi python-gi 
```

### Automount USB
Cuando se inserte un pendrive o una unidad USB en el equipo, este se montara automáticamente utilizando la siguiente configuración. Ademas podemos añadir soporte para NTFS y ExFAT de manera nativa.

```
sudo apt-get install -y usbmount ntfs-3g exfat-fuse
```

To format a exFAT partition:
```
sudo mkfs.exfat -n LABEL /dev/sdXn
```

### Gestion de Discos (EXFat)

Gnomne Partition Manager (GNOME GKT+) `Gparted`. El mejor gestor de particiones para Linux interfaz front-end del gestor `parted` de linea de comando:
```
sudo apt-get install -y gparted
```

### SAMBA 
Share directories as Network Shared Folder using Windows SMB standard. Install Samba PC (Debian / Xubuntu).
```
sudo apt-get install -y samba samba-common-bin
```

Modify Samba configuration adding this options to default settings:
```
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bak
sudo vi /etc/samba/smb.conf
```
```
[global]
netbios name = terminal
workgroup = WORKGROUP
wins support = yes
usershare allow guests = no
```

Set samba password for the default administrator user (usually use default administrator password on system/ssh `raspberry`):
```
sudo smbpasswd -a administrator
```

#### Share a folder in Windows Network (SMB)

Enable write to windows network directories on share directories:
```
[global]
netbios name = Capitan
workgroup = WORKGROUP
wins support = yes
usershare allow guests = yes
map to guest = bad user
guest account = nobody

[compartido]
comment = compartido
path = /opt/compartido
browseable = yes
writeable = yes
create mask = 0775
directory mask = 0775
guest ok = yes
public = yes
```

Enable permissions to folder:
```
sudo mkdir /opt/compartido
sudo chown administrator:administrator -R /opt/compartido
sudo chmod -R 777 /opt/compartido
```

Permitir acceso a los archivos creados desde SMB:
```
sudo groupadd nobody
sudo usermod -a -G nobody administrator
```

### Impresoras Protocolo LPD/LPR

Instalar el sistema CUPS para la gestion de impresoras:
```
sudo apt-get install -y cups libcups2-dev libcupsimage2 libcupsimage2-dev
```

La plataforma `xinetd.d` ayudara para la publicación de servicios TCP/IP desde el terminal y asi poder acceder a las impresoras configuradas de forma remota:
```
sudo apt-get install -y xinetd
```

Configurar el CUPS para administración remota:
```
sudo usermod -aG lpadmin administrator
sudo cupsctl --remote-admin
```

En el archivo  `/etc/cups/cupsd.conf` cambiar en TODOS los bloques que contengan:
```
<Location />
  # Allow remote administration...
  Order allow,deny
  Allow @LOCAL
  Allow all
</Location>
```

Publicar la impresora por protocolo LPD/LPR:
```
sudo vi /etc/xinetd.d/cups-lpd
```
```
service printer
{
        socket_type = stream
        protocol = tcp
        port = 515
        wait = no
        user = lp
        group = sys
        passenv =
        server = /usr/lib/cups/daemon/cups-lpd
        server_args = -o document-format=application/octet-stream
        disable = no
}
```

Los accesos via web del servidor de impresión se realiza via Web utilizando el navegador `palemoon`
```
CUPS
http://xxx.xxx.xxx.xxx:631
```

Una vez realizada la configuración completa reiniciar el servidor de impresión.
```
sudo /etc/init.d/cups restart
```

Una vez reiniciado el sistema ya se podrán acceder a las colas de impresión en toda la red mediante el protocolo LPD/LPR
```
LPD/LPR
IP: xxx.xxx.xxx.xxx (terminal)
Puerto LPD/LPR: 515 (por defecto)
Cola: COLA001 (configurada en CUPS al instalar la impresora)
```

### X-Window Minimal System

Iniciamos la instalación del sistema XWindow con el siguiente repositorio:
```
sudo apt-get install -y --no-install-recommends xserver-xorg xinit
```

### Openbox Window Manager 

Este gestor de ventanas para XWindow es minimalista y ocupa solo 7MB de memoria, optimizado para dispositivos de baja capacidad equipos antiguos. Una vez instalado el OpenBox se puede aplicar el tema `win10mod.obt` con la estética de Windows 10 para las ventanas.
```
sudo apt-get install -y openbox
```

Para iniciar XWindow manualmente utilizar el siguiente comando:
```
startx
```

### XTerminal System
Installation de la consola box en modo grafico Xterm:
```
sudo apt-get install -y xterm
```

Una vez instalada podemos mejorar la consola ajustando colores y fuente de letra desde el siguiente archivo. 
```
vi /home/administrator/.Xresources
vi /home/administrator/.Xdefaults
```

IMPORTANTE!! DEJAR un espacio al principio y al final del archivo!
```

XTerm*background: black
XTerm*foreground: WhiteSmoke
XTerm*faceSize: 11
XTerm*faceName: DejaVu Sans Mono
XTerm*renderFont: true
XTerm*selectToClipboard: true
XTerm*geometry: 100x30
XTerm*VT100.Translations: #override \
                 Ctrl<Key>V:insert-selection(CLIPBOARD) \n\
                 Ctrl<Key>C:copy-selection(CLIPBOARD) \n\
                 Ctrl<Key>X:string(0x03)

```

Al modificar la configuracion se puede recargar usando:
```
sudo apt-get install -y xrdp
xrdb -merge ~/.Xresources
```

Configurar XTerm como terminal de consola por defecto:
```
sudo update-alternatives --config x-terminal-emulator
```

```
There are 4 choices for the alternative x-terminal-emulator (providing /usr/bin/x-terminal-emulator).
  Selection    Path                 Priority   Status
------------------------------------------------------------
* 0            /usr/bin/lxterm       30        
mode
  1            /usr/bin/koi8rxterm   20        manual mode
  2            /usr/bin/lxterm       30        manual mode
  3            /usr/bin/uxterm       20        manual mode
  4            /usr/bin/xterm        20        manual mode

Press enter to keep the current choice[*], or type selection number: 4
```

### VNC Server

I got it working using x11vnc rather than tightvncserver or vncserver
```
sudo apt-get install -y xfonts-75dpi xfonts-100dpi
```

This post set me on track. So first I connect to my computer using ssh
```
ssh administrator@terminal
```

Install x11vnc
```
sudo apt-get -y install x11vnc
```

Create a password for your user:
```
x11vnc -storepasswd
```

Start the X and then start x11vnc
```
starts &
x11vnc -usepw -repeat -shared -forever &
```

La opción -repeat permite tener pulsada una tecla en remoto por repetición.

Se puede poner el comando `x11vnc &` directamente en OpenBox `/home/administrator/.config/openbox/autostart.sh`
Now on my Mac I open the Finder and hit CMD + K and connect to my computer using vnc://XXX.XXX.XXX.XXX

Find Out VNC Port

Type the following command:
```
# sudo netstat -tulp | grep vnc
```

Conectar al puerto VNC:
```
vnc://127.0.0.1:5900
```

### Java and Maven Development Tools

Linux Java Virtual Machine (32/64bits)

```
jdk-8u101-linux-arm32-vfp-hflt.tar.gz
sudo mkdir /usr/lib/jvm
mv jdk-8u101-linux-arm32-vfp-hflt.tar.gz /usr/lib/jvm
tar zxvf jdk-8u101-linux-arm32-vfp-hflt.tar.gz
mv *** jdk8
```

1. Download the Binary tar.gz version the maven website. Pick the latest version `wget`

```
wget https://archive.apache.org/dist/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz
```

2. Extract the archive and install on `/usr/lib/mvn/maven3`

```
sudo mkdir /usr/lib/mvn
sudo tar zxvf apache-maven-3.3.9-bin.tar.gz
sudo mv apache-maven-3.3.9 /usr/lib/mvn/maven3
```

4. Install on system using global vars. At the end of the archive: `/etc/bash.bashrc`

```
# Tell shell where to find java on system profile settings available to all users
export JAVA_HOME=/usr/lib/jvm/jdk8
export PATH=$PATH:$JAVA_HOME/bin
```

```
# Tell shell where to find java and maven on system profile settings available to all users
export M2_HOME=/usr/lib/mvn/maven3
export JAVA_HOME=/usr/lib/jvm/jdk8
export PATH=$PATH:$JAVA_HOME/bin:$M2_HOME/bin
```

### SLiM login manager 

Debian booting into GUI
```
sudo apt-get install -y slim
```

Configurar SLIM para entrar directamente con usuario por defecto `administrator`: 
```
sudo vi /etc/slim.conf
```
```
# default user, leave blank or remove this line
# for avoid pre-loading the username.
default_user        administrator

# Automatically login the default user (without entering
# the password. Set to "yes" to enable this feature
auto_login          yes
```

### VirtualBox Guest Additions

Instalar los drivers para VirtualBox:
```
sudo apt-get install -y build-essential linux-headers-`uname -r` dkms
```

Desde VirtualBox montar el CDROM con los drivers. Copiar los drivers al disco para realizar la instalacion manualmente desde consola.
Asegurarse que aceleracion 2D y 3D y mermoria grafica a 128MB para sporte OpenGL.

Agregar al usuario actual al sistema de montaje para acceder a la carpeta compartida:
```
sudo adduser administrator vboxsf
```

## GTK2/GTK3

Utilidad para gestion de temas lxappearance para GTK2/3. Instalar con la utilidad lxappearence o usar la propia del XFCE.
```
sudo apt-get install -y lxappearance
```

Temas para GTK2/GTK3
```
Mist
Arc-OSX-MID-XFCE-edition
```

Instalar el tema y configurar OpenBox:
```
Win10_mod
```

Temas para XFC4WM
```
Ninix 
```

Iconos de sistema colecciocn FlatRemix
```
git clone https://github.com/daniruiz/Flat-Remix.git
cp Flat-Remix /user/share/icons/
```

### OpenBox Setup

Configuracion de keyboard de `/home/administrator/.config/openbox/rc.xml` para OpenBox y establecer la configuracion minima optimizada:

```
  <keyboard>
    <chainQuitKey>C-g</chainQuitKey>
    <!-- Customize Keybindings -->
    <keybind key="C-w">
      <action name="Close"/>
    </keybind>    
    <keybind key="C-Tab">
      <action name="NextWindow"> 
        <finalactions>
          <action name="Focus"/>
          <action name="Raise"/>
          <action name="Unshade"/>
        </finalactions>
      </action>
    </keybind>
    <keybind key="C-S-Tab">
      <action name="PreviousWindow">
        <finalactions>
          <action name="Focus"/>
          <action name="Raise"/>
          <action name="Unshade"/>
        </finalactions>
      </action>
    </keybind>        
    <keybind key="W-Right">
      <action name="DesktopRight">
        <wrap>no</wrap>
      </action>
    </keybind>
    <keybind key="W-Left">
      <action name="DesktopLeft">
        <wrap>no</wrap>
      </action>
    </keybind>
    <keybind key="W-Up">
      <action name="DesktopRight">
        <wrap>no</wrap>
      </action>
    </keybind>
    <keybind key="W-Down">
      <action name="DesktopLeft">
        <wrap>no</wrap>
      </action>
    </keybind>     
    <keybind key="C-S-4">
      <action name="Execute">
        <command>deepin-scrot</command>
      </action>
    </keybind>
  </keyboard>
```

### OpenBox Autoinicio

Las inicializaciones se puede incorporar al archivo `.xinitrc`. Configuration de OpenBox permite mediante un archivo ejecutar automáticamente scripts y programas para preparar el entorno gráfico antes de inicializarlo.

El archivo `/home/administrator/.config/openbox/autostart.sh` contiene todos los comandos que se ejecutaran automáticamente al iniciar sesión una vez OpenBox se haya inicializado:

```
# Openbox autostart.sh
# Programs that will run after Openbox has started

# Keyboard Swap Ctrl-Cmd
xmodmap .Xmodmap &

# VNC Server
x11vnc -usepw -repeat -shared -forever &

# Windows Envoronment
dbus-launch pcmanfm --desktop &
tint2 &

# Cerebro Spoolight
cerebro
```

### Autostart Apps XWindow (Metodo oficial)

The `~/.xinitrc`, `~/.xsessionrc` file is located in your home directory and it is a hidden file. Here is an example of a default ~/.xsessionrc file: 

```
#!/bin/bash
#
# ~/.xsessionrc
#
# Executed by startx (run your window manager from here)

# Keyboard AppleLayout
setxkbmap -option altwin:ctrl_win

# Touchpad NaturalScroll
synclient VertScrollDelta=-235

# VNC Server (se pueden meter otros servicios de inicio automatico)
x11vnc -usepw -repeat -shared -forever &
```

### Touch Gestures MacBook TouchPad

Install Touchégg and enable OS-X like gestures
```
sudoapt-get install touchegg
https://github.com/JoseExposito/touchegg
```

```
<touchégg>
    
    <settings>
        <property name="composed_gestures_time">0</property>
    </settings>

    <application name="All">
            
        <gesture type="TAP" fingers="3" direction="">
            <action type="MOUSE_CLICK">BUTTON=3</action>
        </gesture>
		        
        <gesture type="DRAG" fingers="3" direction="LEFT">
            <action type="SEND_KEYS">Alt+Control+Left</action>
        </gesture>
        
        <gesture type="DRAG" fingers="3" direction="RIGHT">
            <action type="SEND_KEYS">Alt+Control+Right</action>
        </gesture>

    </application>

</touchégg>
```

### Minimize All-Window Command

```
sudo apt-get install -y wmctrl
```

Respected by Openbox which is the window manager:
```
wmctrl -k on
```

### Intercambio de teclas CTRL+CMD

#### Metodo 1. Crear una archivo para realizar el intercambio (swap) de las teclas CTRL con CMD (WIN).
```
vi /home/administrator/.Xmodmap
```
```
remove control = Control_L
remove mod4 = Super_L 
keysym Control_L = Super_L
keysym Super_L = Control_L
add control = Control_L
add mod4 = Super_L
```

El archivo se carga cada vez que se inicie el sistema en `/home/administrator/.config/openbox/autoload.sh`

```
# keyboard swap ctrl-cmd
xmodmap .Xmodmap &
```

#### Metodo 2. Utilizar la aplicacion `setxkbmap`:

```
# Keyboard AppleLayout
setxkbmap -option altwin:ctrl_win
```

### MacbookPro Function-Keys

Por defecto las telcas de funcion `Fn` vienen activas, para desactivarlas y usar `F1...F12`:
```
echo options hid_apple fnmode=2 | sudo tee -a /etc/modprobe.d/hid_apple.conf
sudo update-initramfs -u -k all
```

## System Utilities Tools

### PCManFM System File Browser

```
sudo apt-get install --no-install-recommends gvfs-backends gvfs-fuse
```

Compilacion manual del PCManFM:

```
https://blog.lxde.org/category/pcmanfm/
https://sourceforge.net/projects/pcmanfm/
```

```
tar xvf libfm-1.2.5.tar.xz
cd libfm-1.2.5
./configure
sudo make install
```  
```
tar xvf pcmanfm-1.2.5.tar
cd pcmanfm-1.2.5
./configure
sudo make install
```

Fondo de escritorio. Cuando se ejecuta PCManFM en modo desktop:
```
pcmanfm --desktop
```

Se puede asignar una imagen de escritorio. Esta se copia en recursos/shared de imagenes de GTK:

```
mkdir /usr/share/wallpapers
cp background.jpg /usr/share/wallpapers
```

### FSearch Busqueda en Sistema

A fast file search utility for Unix-like systems based on GTK+3
```
http://www.fsearch.org
git clone https://github.com/cboxdoerfer/fsearch.git
./configure
sudo make install
```

### Tint2 TaskBar
Tint2 nos facilita una barra de tareas donde se mostraran las ventanas abiertas del sistema de ventanas.

```
sudo apt-get install -y tint2
```

Mediante Tint2 podemos configurar diferentes iconos para lanzar aplicaciones, con el archivo `/home/administrator/.config/tint2/tint2rc`.

```
# Tint2 config file
# Generated by tintwizard (http://code.google.com/p/tintwizard/)
# For information on manually configuring tint2 see http://code.google.com/p/tint2/wiki/Configure

#---------------------------------------------
# BACKGROUND AND BORDER
#---------------------------------------------

rounded = 0
border_width = 0
background_color = #000000 100
border_color = #ffffff 0

rounded = 0
border_width = 0
background_color = #5c90b8 20
border_color = #ffffff 50

rounded = 0
border_width = 0
background_color = #8e857c 30
border_color = #FFFFFF 50

rounded = 0
border_width = 0
background_color = #8e857c 30
border_color = #ffffff 50

#---------------------------------------------
# PANEL
#---------------------------------------------
panel_monitor = all
panel_items = TLBC
panel_position = bottom center
panel_size = 100% 28
panel_margin = 0 0
panel_padding = 0 0
font_shadow = 0
panel_background_id = 1
wm_menu = 0
panel_dock = 0
panel_layer = bottom

#---------------------------------------------
# LAUNCHER
#---------------------------------------------
launcher_padding = 0 0 3
launcher_background_id = 0
launcher_icon_size = 24

#---------------------------------------------
# TASKBAR
#---------------------------------------------
taskbar_mode = single_desktop
taskbar_padding = 1 1 2
taskbar_background_id = 0
#taskbar_active_background_id = 0

#---------------------------------------------
# TASKS
#---------------------------------------------
urgent_nb_of_blink = 8
task_icon = 0
task_text = 1
task_maximum_size = 350 24
task_centered = 1
task_padding = 30 0
task_font = Sans 9
task_font_color = #ffffff 60
task_background_id = 3
task_icon_asb = 100 0 0
task_active_background_id = 4
task_active_font_color = #ffffff 60
task_active_icon_asb = 100 0 0

#---------------------------------------------
# MOUSE ACTION ON TASK
#---------------------------------------------
mouse_middle = none
mouse_right = none
mouse_scroll_up = none
mouse_scroll_down = none

#---------------------------------------------
# SYSTRAY
#---------------------------------------------
systray = 0

#---------------------------------------------
# CLOCK
#---------------------------------------------
time1_format = %a %H:%M
time1_font = DejaVu Sans Bold 9
clock_font_color = #ffffff 76
clock_padding = 6 0
clock_background_id = 0
clock_lclick_command = wmctrl -k on

#---------------------------------------------
# BATTERY
#---------------------------------------------
battery = 1
battery_hide = never
bat1_font = DejaVu Sans Bold 9
bat2_font = DejaVu Sans Bold 9
battery_font_color = #ffffff 76
battery_padding = 0 0
battery_background_id = 0

#---------------------------------------------
# LAUNCHER
#---------------------------------------------
launcher_padding = 0 0 3
launcher_background_id = 0
launcher_icon_size = 24

# End of config
```

### Cerebro
Busqueda directa y ejecucion de aplicaciones tipo Spotlight.
```
wget https://github.com/KELiON/cerebro/releases/download/v0.3.0/cerebro_0.3.0_amd64.deb
```
```
sudo dpkg -i cerebro_0.3.0_amd64.deb
sudo apt-get install -f
```

FIX: Existen problemas con los estilos de fuente por defecto para corregirlo se utiliza el sistema fontconfig nativo de XWindow y se indica la fuente Sans-Serif como fuente de sistema:

```
fc-match --verbose system
fc-cache -f; sudo fc-cache -f
``` 
 
```
>> /home/administrator/.config/fontconfig/fonts.conf
<?xml version='1.0'?>
<!DOCTYPE fontconfig SYSTEM 'fonts.dtd'>
<fontconfig>
  <alias>
    <family>system</family>
    <prefer><family>DejaVu Sans</family></prefer>
  </alias>
</fontconfig>
```

### Editor Textos
Un editor de texto muy sencillo y potente, dispone de un navegador de archivos para localizar cualquier fichero y editarlo.
```
sudo apt-get install -y medit 
```

Configurar medit para cerrar archivos al cerrar el editor.
```
Options Remove Session Suport
```
### Deepin-Scrot

Utilidad para realizar capturas de regiones de pantalla con atajo de teclado:
```
sudo apt-get install -y python-gtk2 python-xlib
```
```
http://packages.linuxdeepin.com/deepin/pool/main/d/deepin-scrot/deepin-scrot_2.0-0deepin_all.deb
sudo dpkg -i deepin-scrot_2.0-0deepin_all.deb 
sudo apt-get -f install
```

### Unrar Descrompressor
Getting rar and unrar working. Download and unpack. Compile source code:
```
wget http://www.rarlab.com/rar/unrarsrc-4.2.4.tar.gz
gunzip -v unrarsrc-4.2.4.tar.gz 
tar -xvf unrarsrc-4.2.4.tar
make -f makefile.unix
```

Install application:
```
sudo cp unrar /usr/local/bin
```

### XArchive 
```
sudo apt-get install -y xarchiver
```

### GTK+ file viewer (Viewnior)

Visor de archivos de imagenes galeria
```
http://siyanpanayotov.com/project/viewnior
sudo apt-get install -y viewnior
```

### Tilda (Terminal DropDown)

```
https://github.com/lanoxx/tilda
```

```
sudo apt-get install -y autopoint libvte-2.91-dev libconfuse-dev
./autogen.sh
./configure
sudo make install
```

### Navegadores Internet Browsers

Google Chrome
```
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i google-chrome-stable_current_amd64.deb
sudo apt-get -f install
```

Mozilla Firefox
```
vi /etc/apt/sources.list
deb http://packages.linuxmint.com debian import
```

```
sudo apt-get update
sudo apt-get install -y firefox
```

### Eclipse
```
/opt/eclipse
```

### Printed Circuit Board Layout Tool (PCB)
```
https://sourceforge.net/projects/pcb/?source=typ_redirect
```

### Limpieza Sistema de Paquetes (APT-GET)
After install all dependences libraries its recommended purge system:
```
sudo apt-get -y autoremove --purge
sudo apt-get autoclean
sudo apt-get autoremove
sudo apt-get clean
sudo ldconfig
```
