# Загрузка Linux

Необходимо попасть в систему без пароля несколькими способами.

### Способ 1. init=/bin/sh

В конце строки начинающейся с **linux16** добавляем **init=/bin/sh** и нажимаем **сtrl-x** для загрузки в систему

![1](https://user-images.githubusercontent.com/110412814/216019713-522b0198-e084-4324-9fd7-8eb209ecc745.png)


Рутовая файловая система при этом монтируется в режиме **Read-Only**. Если вы хотите перемонтировать ее в режим **Read-Write** можно воспользоваться командой: 


# mount -o remount,rw /


Проверяем созданием файла, смонтировался ли **root**, с правами на запись:

![2](https://user-images.githubusercontent.com/110412814/216019767-2f5b1e1b-ae61-4b93-b9e1-419d774a7d0e.png)


### Способ 2. rd.break

В конце строки начинающейся с **linux16** добавляем **rd.break** и нажимаем **сtrl-x** для загрузки в систему

![3](https://user-images.githubusercontent.com/110412814/216019784-bdea5c1b-a5cd-4cb9-b811-493772173c10.png)


Попадаем в Emergency mode, система при этом монтируется в режиме **Read-Only**. Меняем пароль от **root** 

![4](https://user-images.githubusercontent.com/110412814/216019822-ea2fcee7-9cfb-48cc-b32b-70352026f308.png)


### Способ 3. rw init=/sysroot/bin/sh

В конце строки начинающейся с **linux16** заменяем **ro** на **rw init=/sysroot/bin/sh** и нажимаем **сtrl-x** для загрузки в систему

### Переименовываем VG

Ход этой части ДЗ записан scrypt 

Смотрим текущее состояние


[root@bootVM ~]# vgs
  VG         #PV #LV #SN Attr   VSize   VFree
  VolGroup00   1   2   0 wz--n- <38.97g    0 


Переименовываем VG


[root@bootVM ~]# vgrename VolGroup00 OtusRoot
  Volume group "VolGroup00" successfully renamed to "OtusRoot"


Далее правим **/etc/fstab, /etc/default/grub, /boot/grub2/grub.cfg**. Везде заменяем старое название на новое. 

Пересоздаем **initrd image**, чтобý он знал новое название **Volume Group**


[root@bootVM ~]# mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
*** Creating image file done ***
*** Creating initramfs image file '/boot/initramfs-3.10.0-862.2.3.el7.x86_64.img' done ***


Проверяем


[root@bootVM ~]# vgs
  VG       #PV #LV #SN Attr   VSize   VFree
  OtusRoot   1   2   0 wz--n- <38.97g    0 


### Добавление модуля в initrd

Скрипты модулей хранāтся в каталоге **/usr/lib/dracut/modules.d/**. Для того чтобы добавить свой модуль создаем там папку с именем **01test** и скопируем туда скрипты нашего мудуля


[root@bootVM ~]# cd /usr/lib/dracut/modules.d
[root@bootVM modules.d]# mkdir 01test
[root@bootVM modules.d]# cd 01test/
[root@bootVM 01test]# cp /tmp/module-setup.sh ./
[root@bootVM 01test]# cp /tmp/test.sh ./


Пересобираем образ initrd


[root@bootVM 01test]# dracut -f -v

*** Creating image file ***
*** Creating image file done ***
*** Creating initramfs image file '/boot/initramfs-3.10.0-862.2.3.el7.x86_64.img' done ***


Выключаем опции **rghb** и **quiet** отредактировав файл **grub.cfg**.

Перезагружаемся, проверяем

![6](https://user-images.githubusercontent.com/110412814/216020067-8d49960b-60b0-4ca4-9820-ac41f80e1fff.png)

