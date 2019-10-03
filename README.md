
# 1
Preinstalación

Las imágenes de instalación y sus firmas GnuPG (Español) se pueden obtener desde la página de descargas.
Verificar las firmas

Se recomienda verificar la firma de la imagen antes de usarla, especialmente cuando se descarga desde un servidor de réplica HTTP, donde las descargas generalmente son susceptibles de ser interceptadas por imágenes de servidores maliciosos.

En un sistema con GnuPG (Español) instalado, realice esta operación descargando la firma PGP (bajo Checksums) en el directorio de la imagen ISO, y verifíquela con la siguiente orden:

$ gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig

También, desde una instalación existente de Arch Linux, puede escribir:

$ pacman-key -v archlinux-version-x86_64.iso.sig

Nota:

    La firma en sí podría manipularse si se descarga desde un sitio de réplica, en lugar de hacerlo desde archlinux.org como se recomendó antes. En este caso, asegúrese de que la clave pública, que se utiliza para decodificar la firma, esté firmada por otra clave de confianza. La orden gpg emitirá la huella digital de la clave pública.
    Otro método para verificar la autenticidad de la firma es asegurarse de que la huella digital de la clave pública sea idéntica a la huella digital de la del desarrollador de Arch Linux que firmó el archivo ISO. Consulte Wikipedia:Public-key cryptography y Wikipedia:es:Criptografía asimétrica para obtener más información sobre el proceso de clave pública para autenticar claves.

Arrancar el entorno live

El entorno live se puede iniciar desde una unidad flash USB, un disco óptico o una red con PXE (Español). Para medios de instalación alternativos, vea Category:Installation process (Español).

    La indicación del dispositivo de arranque actual en una unidad que contenga el soporte de instalación de Arch se logra, normalmente, presionando una tecla durante la fase POST (siglas en inglés de «power-on self-test» o autoprueba de arranque), tecla que se suele indicar en la pantalla de inicio del ordenador. Consulte el manual de su placa base para más detalles.
    Cuando aparezca el menú de Arch, seleccione Boot Arch Linux y presione Intro para ingresar al entorno de instalación.
    Consulte README.bootparams para obtener una lista de los parámetros de arranque, y packages.x86_64 para obtener una lista de los paquetes incluidos (en la imagen).
    Iniciará sesión en la primera consola virtual como usuario root, y se le presentará un intérprete de línea de órdens Zsh (Español).

Para cambiar a una consola diferente —por ejemplo, para ver esta guía con el navegador web ELinks junto a la instalación—, utilice el atajo del teclado Alt+flecha . Para editar los archivos de configuración, dispone de los editores nano, vi y vim.
Definir la distribución del teclado en el entorno live

Por defecto, la distribución del teclado de la consola es la de EE.UU.. Las distribuciones de teclado disponibles se pueden enumerar con:

# ls /usr/share/kbd/keymaps/**/*.map.gz

La distribución del teclado se puede cambiar con la orden loadkeys(1), añadiendo el nombre de un archivo (no es necesario especificar la ruta ni la extensión del archivo cuando se usa «loadkeys»). Por ejemplo, para establecer una distribución de teclado en español, ejecute:

# loadkeys es

Los tipos de letras para consola se encuentran en /usr/share/kbd/consolefonts/ y se pueden configurar igualmente con setfont(8).
Verificar la modalidad de arranque

Si el modo UEFI está activado en una placa base UEFI (Español), Archiso (Español) arrancará en consecuencia a través de systemd-boot (Español). Para comprobar esto, liste el contenido del directorio efivars:

# ls /sys/firmware/efi/efivars

Si no existe el directorio, el sistema se iniciará en modo BIOS o CSM (Compatibility Support Module). Remítase al manual de su placa base para obtener detalles.
Conectarse a Internet

Para configurar una conexión de red, siga los siguientes pasos:

    Asegúrese de que su interfaz de red está en la lista y activada, por ejemplo con la orden ip-link(8):

    # ip link

    Conéctese a la red. Enchufe el cable Ethernet o conéctese a una LAN inalámbrica.
    Configure su conexión de red:
        Dirección IP estática.
        Dirección IP dinámica: utilice DHCP.

        Nota: la imagen de la instalación activa en el arranque dhcpcd (dhcpcd@nombredelainterfaz.service) para los dispositivos de red cableados.

    La conexión se puede verificar con la utilidad ping:

    # ping archlinux.org

Actualizar el reloj del sistema

Utilice timedatectl(1) para asegurarse de que el reloj del sistema sea preciso:

# timedatectl set-ntp true

Para comprobar el estado del servicio, utilice timedatectl status.
Particionar el disco

Cuando el sistema lo reconoce, los discos se asignan a un dispositivo de bloque como /dev/sda o /dev/nvme0n1. Para identificar estos dispositivos, utilice lsblk o fdisk:

# fdisk -l

Los resultados que terminan en rom, loop o airoot pueden ignorarse.

Las siguientes particiones son necesarias para el dispositivo elegido:

    Una partición para el directorio raíz /.
    Si UEFI está activado, una partición del sistema EFI.

Si desea crear dispositivos de bloques apilados —stacked block devices— para LVM (Español), cifrado de disco o RAID (Español), hágalo ahora.
Esquemas de ejemplo
BIOS con MBR
Punto de montaje 	Partición 	Tipo de partición 	Tamaño sugerido
/mnt 	/dev/sdX1 	Linux 	Resto del dispositivo
[SWAP] 	/dev/sdX2 	Linux swap 	Más de 512 MiB
UEFI con GPT
Punto de montaje 	Partición 	Tipo de partición 	Tamaño sugerido
/mnt/boot o /mnt/efi 	/dev/sdX1 	EFI system partition 	260–512 MiB
/mnt 	/dev/sdX2 	Linux x86-64 root (/) 	Resto del dispositivo
[SWAP] 	/dev/sdX3 	Linux swap 	Más de 512 MiB

Véase también Partitioning (Español)#Esquemas de particionado.
Nota:

    Utilice fdisk (Español) o parted para modificar la tabla de particionado, por ejemplo, fdisk /dev/sdX.
    El espacio de intercambio se puede configurar en un archivo de intercambio para los sistemas de archivos que lo admitan.

Formatear las particiones

Una vez se han creado las particiones, cada una de ellas debe formatearse con un sistema de archivos adecuado. Por ejemplo, si la partición raíz está en /dev/sdX1 y se ha pensado en ext4 como su sistema de archivos, ejecute:

# mkfs.ext4 /dev/sdX1

Si creó una partición para el espacio de intercambio (swap), formatéela con mkswap:

# mkswap /dev/sdX2
# swapon /dev/sdX2

Consulte cómo crear un sistema de archivos para más detalles.
Montar los sistemas de archivos

El siguiente paso es montar el sistema de archivos de la partición raíz —root— en /mnt, por ejemplo:

# mount /dev/sdX1 /mnt

Cree los puntos de montaje restantes (como /mnt/efi) y monte las particiones correspondientes en ellos.

Los sistemas de archivos montados, así como el espacio de intercambio, serán posteriormente detectados por genfstab.
Instalación
Seleccionar los servidores de réplica

Los paquetes que se instalarán se deben descargar desde los servidores de réplicas, que se definen en /etc/pacman.d/mirrorlist. En el entorno live de instalación, todos los servidores de réplicas están activados y ordenados de acuerdo al estado de sincronización y velocidad en el momento de creación de la imagen de instalación.

Cuanto más alto se coloca un servidor de réplica en la lista, más prioridad tendrá al descargar un paquete. Es posible que desee modificar el archivo en consecuencia y mover los servidores de réplicas geográficamente más cercanos, a la parte superior de la lista, aunque se deben tener en consideración otros criterios.

Una copia del archivo «mirrorlist» se realizará más tarde en el nuevo sistema por pacstrap, por lo tanto vale la pena hacerlo bien en esta fase.
Instalar los paquetes del sistema base

Utilice el script pacstrap para instalar el grupo de paquetes base:

# pacstrap /mnt base

Este grupo de paquetes no incluye todas las herramientas disponibles en el entorno live de instalación, como son los casos de btrfs-progs o firmware inalámbrico específico; consulte paquetes.x86_64 para ver la comparación.

Para instalar otros paquetes o grupos de paquetes, como base-devel, en el nuevo sistema, añada sus nombres a la orden pacstrap (separados por un espacio) o, posteriormente a la etapa de #Chroot, con órdenes individuales con pacman (Español).
Configuración del sistema
Fstab

Genere un archivo fstab (Español) (utilice -U o -L para especificar en dicho archivo las UUID o las etiquetas, respectivamente):

# genfstab -U /mnt >> /mnt/etc/fstab

Compruebe el archivo resultante en /mnt/etc/fstab después, y modifíquelo en caso de errores.
Chroot

Cambie la raíz al nuevo sistema:

# arch-chroot /mnt

Zona horaria

Defina su zona horaria:

# ln -sf /usr/share/zoneinfo/Región/Ciudad /etc/localtime

Ejecute hwclock(8) para generar el archivo /etc/adjtime:

# hwclock --systohc

Esta orden presume que le reloj del hardware esta configurado en UTC. Vea la sección sobre la hora estándar para obtener más detalles.
Idioma del sistema

Descomente el locale (Español) necesario (por ejemplo en España sería es_ES.UTF-8 UTF-8) en /etc/locale.gen, además de en_US.UTF-8 UTF-8 y, después, genérelo con la orden:

# locale-gen

Cree el archivo locale.conf(5), y defina la variable LANG en locale.conf(5) según su caso, por ejemplo:

/etc/locale.conf

LANG=es_ES.UTF-8

Si fuese necesario, defina la distribución de teclado en vconsole.conf(5) para que permanezca en cada reinicio:

/etc/vconsole.conf

KEYMAP=es

Configurar la red

Cree el archivo hostname:

/etc/hostname

elnombredemiequipo

Considere añadir una entrada similar en hosts(5):

/etc/hosts

127.0.0.1	localhost
::1		localhost
127.0.1.1	elnombredemiequipo.localdomain	elnombredemiequipo

Si el sistema tiene una dirección IP permanente, se debe usar dicha dirección, en lugar de 127.0.1.1.

Configure la conexión de red de nuevo para el entorno recién instalado.
Initramfs

Normalmente no es necesario crear una imagen initramfs nueva, dado que mkinitcpio (Español) se ejecuta durante la instalación del paquete linux con pacstrap.

Para LVM, cifrado del sistema o RAID, modifique mkinitcpio.conf(5) y regenere la imagen initramfs:

# mkinitcpio -p linux

Contraseña de root

Establezca la contraseña de root:

# passwd

Instalar gestor de arranque

Elija e instale un gestor de arranque compatible con Linux. Si tiene una CPU Intel o AMD, active las actualizaciones de microcode (Español).
Reiniciar

Salga del entorno chroot escribiendo exit o presionando Ctrl+d.

Opcionalmente, puede desmontar manualmente todas las particiones con umount -R /mnt: esto permite advertir cualquier partición «ocupada», y buscar su causa con fuser(1).

Por último, reinicie el equipo escribiendo reboot: cualquier partición que todavía esté montada será desmontada automáticamente por systemd. Recuerde retirar los medios de instalación y luego inicie sesión en el nuevo sistema con la cuenta de root.
Posinstalación

Vea el artículo General recommendations (Español) para obtener instrucciones sobre cómo gestionar el sistema, así como tutoriales sobre qué hacer después de la instalación (tales como configurar una interfaz gráfica de usuario, el sonido o un panel táctil).

Para obtener una lista de aplicaciones que pueden ser de su interés, consulte List of applications (Español). 
