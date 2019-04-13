```
    Este archivo es parte de FLISol USB MultiBoot disk

    flisol-usbmulti is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.

    Foobar is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with Foobar.  If not, see <http://www.gnu.org/licenses/>.
```

## Instrucciones y archivos para crear un disco de instalación multipropósito

## Objetivos
Generar una unidad de bloques (preferentemente pen-drive o disco USB) desde el cual poder iniciar una computadora en modo Legacy BIOS o EFI y cargar una distribución de GNU/Linux desde un menú interactivo.

Este proyecto contiene todas las instrucciones y archivos de configuración necesarios para crear la unidad previamente indicada.

## Requisitos previos
Para poder crear la unidad vas a necesitar:
 * Sistema operativo Debian GNU/Linux
 * git (para copiar todo este repositorio en vez de copiar archivo por archivo)
 * wget o cliente bittorrent (para descargar archivos .iso de cada distribución)
 * una máquina de pruebas (nunca de producción) o una máquina virtual sin datos relevantes, desde la cual correr este instructivo experimental que pone en riesgo cualquier información en ella almacenada. La máquina de pruebas o máquina virtual debe tener una distribución Debian GNU/Linux de 64 bits en modo UEFI, recién instalada y sin datos
 * un pen-drive o disco USB externo con suficiente espacio (unos 8GB) para alojar los archivos de las diferentes distribuciones. Toda la información almacenada en esa unidad será eliminada irrevocablemente
 * un cerebro marginalmente funcional (casi todos tenemos uno)

Si bien esta guía se puede aplicar utilizando otras distribuciones GNU/Linux, elegimos Debian por cuestiones prácticas.

## Replicando este repositorio
```
git clone https://github.com/fefulowe/flisol-usbmulti
```

[inicio del instructivo]
## Inicializar unidad USB
A diferencia de la versión anterior de este proyecto (que era un procedimiento paso a paso manual para crear la unidad), esta nueva versión de disco USB aprovecha el proyecto Multiboot USB https://mbusb.aguslr.com/install.html para automatizar el particionado y copia de archivos a la unidad USB.

##  Definir variables de trabajo
```
export voyaromperestedisco=sdX
export mountpoint=/mnt	# Directorio de montaje para trabajo local
```
Donde <mountpoint> es el directorio que vas a usar para montar la unidad USB (por ejemplo: /media/multiusb1) y <voyaromperestedisco> corresponde a la unidad USB que vamos a reconfigurar (por ejemplo: sdb).

## Limpiando la unidad
```
dd if=/dev/zero of=/dev/$voyaromperestedisco count=4095 # Este comando es un punto sin retorno. Los datos almacenados en el dispositivo ya no se podrán acceder.
```

## Replicando el repositorio multibootusb
```
git clone https://github.com/aguslr/multibootusb
cd multibootusb
```

## Particionando y copiando archivos grub
```
./makeUSB.sh --hybrid /dev/$voyaromperestedisco ext4
sudo echo 1 > /sys/block/$voyaromperestedisco/device/rescan
```

## Particionando y copiando archivos grub
Ahora solamente resta montar la unidad y descargar los archivos .iso a la carpeta /boot/isos . Orientativamente podemos revisar el listado de archivos con suma de verificación de la carpeta (/boot/isos/SHA256.txt). Recomendamos hacer la suma de verificación de cada uno de los archivos descargados y constatar que concuerde exactamente con los valores publicados en el sitio web de cada distribución.
```
mount /dev/"$voyaromperestedisco"3 $mountpoint
cd $mountpoint/boot/isos
cat $mountopint/boot/isos/SHA256.txt
wget --continue https://sourceforge.net/projects/systemrescuecd/files/sysresccd-x86/5.3.2/systemrescuecd-x86-5.3.2.iso/download --output-document systemrescuecd-x86-5.3.2.iso
```

# Instalar paquetes para grub-EFI
En caso de que la instalación presente un error al momento de copiar los archivos de grub-efi, será necesario instalar los paquetes correspondientes.
```
apt-get install grub-efi # Instala el paquete grub-efi-amd64 para entornos EFI
apt-get install grub-splashimages # Instala el paquete para habilitar el modo gráfico
```

# Habilitar la arquitectura i386 pra grub-pc (bios) (derivados debian solamente)
Si el equipo solamente tiene la arquitectura amd64 y queremos agrelarle la arquitectura i386, es probable que tengamos que agregar los paquetes correspondientes.
```
dpkg --add-architecture i386 # probablemente no sea necesario agregar los repositorios i386 para instalar el paquete grub-pc
apt-get update # Actualiza el listado de paquetes
apt-get install grub-pc # Instala el paquete grub-pc
```
[Fin del instructivo]

## Versionado
Empleamos [SemVer](http://semver.org/) para indicar las versiones publicadas. Para ver las versiones disponibles, consultar el [listado de etiquetas](https://github.com/fefulowe/flisol-usbmulti/tags). 

## Descarga de imagen .raw lista para copiar a disco de instalación
La última versión de esta imagen se puede [descargar](http://mgnet.me/.Flisol2018r2018031701) con cualquier cliente bittorrent que soporte enlaces magnet (como Deluge o Transmission).

## Proyectos similares
 * https://github.com/aguslr/multibootusb

## Autores
[Fefu](https://www.fefu.eu)

## Agradecimientos
 * Diabolo
 * HaCkan
 * CaFeLUG
