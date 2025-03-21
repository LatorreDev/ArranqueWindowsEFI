#### 🛠 1. Iniciar desde un USB de instalación de Windows
Descarga la ISO de Windows 11 desde la página oficial.

Crea un USB booteable con Rufus en Windows o con el siguiente comando en Linux:
```
sudo dd if=Windows.iso of=/dev/sdX bs=4M status=progress
```
(Reemplaza /dev/sdX con tu USB correcto, cuidado con sobrescribir otro disco).
Inicia desde la USB en modo UEFI.

#### 🛠 2. Acceder al Símbolo del Sistema en Windows
En la pantalla de instalación de Windows, haz clic en:
_→ "Reparar el equipo" → "Solucionar problemas" → "Opciones avanzadas" → "Símbolo del sistema"._

#### 🛠 3. Identificar la partición EFI existente
Abre diskpart para administrar discos:

```
diskpart
```

Lista los discos conectados:

```
list disk
```
Ubica el disco donde está la partición EFI compartida.
Si tienes tu distro y Windows en discos diferentes, la partición EFI estará en el disco de tu distro.
Selecciona el disco correcto (reemplaza X con el número del disco que contiene la partición EFI):

```
select disk X
```

Lista las particiones en ese disco:

```
list partition
```
Busca la partición EFI (100MB-500MB, FAT32).
Selecciona la partición EFI correcta (reemplaza Y con el número de la partición EFI):

```
select partition Y
```
Asígnale una letra temporal (Ejemplo: Z:):

```
assign letter=Z
```
Salir de diskpart:

```
exit
```

#### 🛠 4. Reparar el boot de Windows en la partición EFI compartida
Verifica que la partición EFI tiene tu distro instalada:

```
dir Z:\EFI
```
Deberías ver una carpeta con el nombre de tu distro y quizás otra de "Microsoft" (si Windows estuvo antes en esta partición).
Reinstalar el bootloader de Windows en la misma partición EFI:

```
bcdboot C:\Windows /s Z: /f UEFI
```
Explicación:

C:\Windows **→** Ubicación del sistema operativo.
/s Z: **→** Instala el bootloader en la partición EFI Z:.
/f UEFI **→** Fuerza el modo UEFI (sin MBR).
Verifica que se creó la carpeta EFI/Microsoft en Z::

```
dir Z:\EFI
```
Si ves Microsoft junto a tu distro, significa que Windows Boot Manager está instalado en la partición EFI correcta.

#### 🛠 5. Restaurar GRUB y asegurarse de que tu distro y Windows aparezcan en el menú
Reinicia y entra en tu distro usando el modo de arranque del BIOS (F12, ESC, F8 dependiendo de tu placa base).
Actualiza GRUB para que detecte Windows:
```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```
Si Windows no aparece en GRUB, instala os-prober y vuelve a generar GRUB:

```
sudo dnf install os-prober -y

sudo grub-mkconfig -o /boot/grub/grub.cfg
```


#### **🚀 ¡Tu sistema está reparado y listo para seguir trabajando! 🎯**