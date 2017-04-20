```
    Este archivo es parte de FLISol USB MultiBoot disk

    Foobar is free software: you can redistribute it and/or modify
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
 * una máquina de pruebas (nunca de producción) o una máquina virtual sin datos relevantes, desde la cual correr este instructivo experimental que pone en riesgo cualquier información en ella almacenada. La máquina de pruebas o máquina virtual debe tener una distribución Debian GNU/Linux de 64 bits en modo UEFI, recién instalada y sin datos
 * un pen-drive o disco USB externo con suficiente espacio (unos 8GB) para alojar los archivos de las diferentes distribuciones. Toda la información almacenada en esa unidad será eliminada irrevocablemente
 * un cerebro marginalmente funcional (casi todos tenemos uno)

Si bien esta guía se puede aplicar utilizando otras distribuciones GNU/Linux, elegimos Debian por cuestiones prácticas.

## Replicando este repositorio
```
git clone https://github.com/fefulowe/flisol-usbmulti
```

[inicio del instructivo]
##  Definir variables
```
blockdisk=sdupalala # Nombre del dispositivo de bloques a borrar e intervenir
mountpoint=/mnt	# Directorio de montaje para trabajo local
```

## Asignación de constantes en base a variables predefinidas
```
devdisk=/dev/$blockdisk # Unidad USB a reacondicionar
grubdir="$mountpoint"/boot # Este comando se debe usar al 
```

## Inicializar el disco
```
dd if=/dev/zero of=$devdisk count=4095 # Este comando es un punto sin retorno. Los datos almacenados en el dispositivo ya no se podrán acceder.
```

## Creación de tabla de particiones y particiones
```
parted -s $devdisk mklabel gpt
parted -s $devdisk unit s mkpart '"BIOS boot partition"' 2048 4095
parted -s $devdisk set 1 bios_grub on
parted -s $devdisk unit s mkpart '"EFI System"' fat32 4096 100%
parted -s $devdisk set 2 boot on
parted -s $devdisk set 2 esp on
parted -s $devdisk unit s print free
parted -s $devdisk print free
sudo echo 1 > /sys/block/$blockdisk/device/rescan
```

# Create filesystem for EFI partition
```
mkfs.vfat -F32 -n EFIVFAT "$devdisk"2
```

# Mount EFI partition and create boot directory
```
mount "$devdisk"2 $mountpoint
mkdir $grubdir
```

# Instalar paquetes para grub-EFI
```
apt-get install grub-efi # Instala el paquete grub-efi-amd64 para entornos EFI
apt-get install grub-splashimages # Instala el paquete para habilitar el modo gráfico
grub-install --verbose --target=x86_64-efi --efi-directory=$mountpoint --boot-directory=$grubdir --removable --recheck 
```

# Habilitar la arquitectura i386 pra grub-pc (bios) (derivados debian solamente)
```
dpkg --add-architecture i386 # probablemente no sea necesario agregar los repositorios i386 para instalar el paquete grub-pc
apt-get update # Actualiza el listado de paquetes
apt-get install grub-pc # Instala el paquete grub-pc
```

## Preparar la unidad para iniciar con grub en modo Legacy Bios (contenido en el paquete grub)
```
grub-install --verbose --target=i386-pc --recheck --boot-directory=$grubdir $devdisk
```

## Copiar el contenido del presente repositorio
```
cp * $mountpoint
```

## Desmontar la unidad
```
umount $mountpoint # Desmonta la unidad
```
[Fin del instructivo]

## Versionado
Empleamos [SemVer](http://semver.org/) para indicar las versiones publicadas. Para ver las versiones disponibles, consultar el [listado de etiquetas](https://github.com/fefulowe/flisol-usbmulti/tags). 

## Autores
[Fefu](https://www.fefu.eu)

## Agradecimientos
 * Diabolo
 * HaCkan
 * CaFeLUG
