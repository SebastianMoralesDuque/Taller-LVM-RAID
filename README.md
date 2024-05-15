# Guía de Configuración de RAID 1, LVM y OwnCloud


1. Preparar el Entorno
2. Instalar Dependencias
```bash
sudo apt update
sudo apt install -y mdadm lvm2 docker docker-compose
```
3. Crear Discos Virtuales
4. Crear Archivos de Imagen
```bash
sudo dd if=/dev/zero of=/mnt/disk1.img bs=1M count=1024
sudo dd if=/dev/zero of=/mnt/disk2.img bs=1M count=1024
sudo dd if=/dev/zero of=/mnt/disk3.img bs=1M count=1024
```

5. Asociar Archivos de Imagen a Dispositivos de Loop
```bash
sudo losetup /dev/loop0 /mnt/disk1.img
sudo losetup /dev/loop1 /mnt/disk2.img
sudo losetup /dev/loop2 /mnt/disk3.img
```
6. Verifica los dispositivos de loop creados (20, 21 y 22):
```bash
sudo losetup -a
```
7. Configurar RAID 1
8. Crear el RAID 1
```bash
sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=3 /dev/loop0 /dev/loop1 /dev/loop2
```
9. Crear y Configurar LVM
10. Crear un Physical Volume (PV) en el dispositivo RAID:
```bash
sudo pvcreate /dev/md0
```
11. Crear un Volume Group (VG):
```bash
sudo vgcreate vg_owncloud /dev/md0
```
12. Crear un Logical Volume (LV):
```bash
sudo lvcreate -L 512M -n lv_owncloud vg_owncloud
```
13. Formatear el LV con un sistema de archivos:
```bash
sudo mkfs.ext4 /dev/vg_owncloud/lv_owncloud
```
14. Montar el LV en un directorio:
```bash
sudo mkdir /mnt/owncloud
sudo mount /dev/vg_owncloud/lv_owncloud /mnt/owncloud
```
