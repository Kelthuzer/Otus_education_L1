#  LVM
## Уменьшить том под / до 8G
- подготовка тома
  
  ```
  pvcreate /dev/sdb /
  vgcreate vg_root /dev/sdb /
  lvcreate -n lv_root -l +100%FREE /dev/vg_r
  ```
  получаем
  <img width="800" height="134" alt="image" src="https://github.com/user-attachments/assets/5af7fad4-3d5e-4217-89ae-6f3fdfdd46d0" />
- создаем ФС
```
mkfs.ext4 /dev/vg_root/lv_root
```
<img width="800" height="194" alt="image" src="https://github.com/user-attachments/assets/06e22568-2a0e-4dff-8ca7-9eea00df4a26" />
- монтируем `mount /dev/vg_root/lv_root /mnt/root_resize`
- копируем `rsync -avxHAX --progress / /mnt/`, ждем завершения. В итоге получим
  ```
  sent 4,125,280,042 bytes  received 2,853,364 bytes  81,745,215.96 bytes/sec
  total size is 4,118,307,974  speedup is 1.00
  ```
- перепроверяем на всякий случай 
  <img width="532" height="470" alt="image" src="https://github.com/user-attachments/assets/69c7334e-94e3-4c4f-bf48-b2a9ee6edc32" />
- подготавливаем окружение 
```
for i in /proc/ /sys/ /dev/ /run/ /boot/; do mount --bind $i /mnt/root_resize/$i; done
```
- меняем корень `chroot /mnt/root_resize/`
- настройка граб `grub-mkconfig -o /boot/grub/grub.cfg`
  <img width="638" height="249" alt="image" src="https://github.com/user-attachments/assets/6a3a2db0-53c3-494f-aca1-7d8f885567ad" />
- Обновим образ initrd
`update-initramfs: Generating /boot/initrd.img-6.19.0-061900-generic`
- Перезагружаем систему `exit shutdonw -r now`. Получаем ошибку `Running in chroot, ignoring request.` выходим и снова пробуем перезагруку получаем 
  <img width="615" height="126" alt="image" src="https://github.com/user-attachments/assets/da909d7f-8865-4b08-aa1a-c473864274a2" />
- Проверяем наши действия `lsblk`
<img width="447" height="175" alt="image" src="https://github.com/user-attachments/assets/8f74fb8e-6463-4c42-b65e-6129ee21362a" />


