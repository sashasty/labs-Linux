   ЗАДАНИЕ 1
   
    1.Параметры нашей машины
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/1.jpg)

    2. При установке ОС настроили RAID и логические тома(как в пояснении), получили следующую картину:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/2.jpg)

    3.Копируем содержимое раздела /boot с диска sda на sdb и смотрим информацию о дисках с помощью fdisk -l 
    (флаг -l используется для просмотров всех доступных разделов).
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/3.jpg)

   Утилита вывела информацию о дисках(sda, sdb), разделах(sda1, sdb1 и т.д.), которые содержатся на этих 
    дисках, RAID-массиве(md0) и логических томах(root, var, log), которые мы создали.
    
    4.Используем команду lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT, получив следующий результат:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/4.jpg)

   Команда вывела дерево дисковых пространств и инфрмацию(размер, тип файловой системы, тип пространства,
    точка монтирования) о каждой части дерева.
    
    5.Устанавливаем grub на диск sdb командой grub-install /dev/sdb:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/5.jpg)

    6.Просматриваем информацию о физических томах(команда pvs), логических группах(vgs) и логических 
    томах(lvs),mount получаем:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/6.jpg)
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/7.jpg)

   Команда pvs вывела все содержащиеся в системе физические тома(у нас это md0), их размер, атрибуты
    и логическую группу, которую содержит физический том. Команда vgs вывела информацию(кол-во логических
    томов, размер, атрибуты) о единственной логической группе system. Команда lvs показывает группу,
    атрибуты и размер каждого логического тома в системе.
    
    7.Чтобы узнать информацию о RAID открываем для чтения файл /proc/mdstat:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/8.jpg)

   Данная команда показала активные диски в нашем RAID-массиве. В нашем массиве оба объединенных 
    раздела(sda1 и sdb2) функционируют.
    В ходе данного задания мы объединили разделы двух дисков в RAID-массив, получившийся раздел мы
    разбили на логические тома(настроили LVM).
    
   ЗАДАНИЕ 2

    1.Удалили диск ssd1 из машины:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image9.jpg)

   После перезагрузок машина не утратила свою работоспособность.
   
   
    2.Проверим статус массива cat /proc/mdstat, получили следующий вывод:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image10.jpg)

   Как видим, в массиве находится только один раздел(sda2).
   
    3.Подключаем к машине новый диск ssd3, при включении загрузка происходила с диска, номер порта
    которого меньше, а мы не переключали ssd2 к "меньшему" порту, поэтому при загрузке нажимаем на
    F12 и загружаемся со второго диска.
    
    4.полняем команду fdisk -l, которая покажет нам, видит ли ОС новый диск.
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image11.jpg)

   В выводе можно увидеть новый диск(sda) в нем нет разделов. Название прошлого диска изменилось на
    sdb из-за того, что именования дисков строятся по номеру порта.
    
    
    5.Копируем таблицу разделов командой sfdisk -d /dev/sdb | sfdisk /dev/sda, флаг -d позволяет получить
    информацию в формате, ориентированном на применение утилиты sfdisk для изменения таблицы разделов(эту
    утилиту можно использовать просто для получения информации).
    
    
    6.После копирования на sda появились разделы sda1 и sda2.
    
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image12.jpg)

    7.Добавили новый диск в массив:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image13.jpg)

   Вывод команды cat proc/mdstat показывает, что началась синхронизация.
    
    
    8.После синхронизации массива копируем загрузочный раздел на новый диск и устанавливаем grub.
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image14.jpg)

    9.Машина сохранила работоспособность после перезагрузки.


    В ходе задания, устроив отказ одного из дисков RAID-массива(ssd1), мы добавили в RAID новый диск
    ssd3 и синхронизировали его с ssd2.
    
ЗАДАНИЕ 3

    1.Удалили диск ssd2
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image16.jpg)

    2.После перезагрузки система работает, узнаем о состоянии RAID-массива.

Через mdstat и fdisk:

![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image17.jpg)

    3.lsblk:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image18.jpg)

Удаленный диск пропал, в массиве присутствует только 1 диск.

    4.После добавления нового ssd4 утилиты выводят следующее:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image19.jpg)
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image20.jpg)

Машина видит новый диск. Старый диск не переименовался, так как его порядковый номер порта SATA был меньше чем у нового диска.

    5.Перенос таблицы разделов на новый диск, создание нового RAID-массива.

   Копируем файловую таблицу со старого диска на новый командой sfdisk -d /dev/sda | sfdisk /dev/sdb, данная команда берет таблицу разделов диска sda(ssd3) в удобном для копирования формате(флаг -d) и направляет эту информацию на замену существующей таблице диска sdb(ssd4).
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image21.jpg)

   Команда lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT показывает то, что копирование таблицы разделов прошло успешно(на sdb диске появились разделы).
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image22.jpg)

   Далее мы копируем данные /boot на новый диск командой dd if=/dev/sda1 of=/dev/sdb1 и перемонтируем /boot на новый диск используя утилиты unmount и mount.
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image25.jpg)

Получили /boot, который примонтирован на новый ssd.

   Чтобы закончить работу с /boot устанавливаем grub командой grub-install /dev/sdb, команда установки grub выполняется для того, чтобы при отключении или выходе из строя одного из дисков у нас был загрузчик для запуска системы и продолжения работы.

   Создадим новый массив командой mdadm --create --verbose /dev/md63 --level=1 --force --raid-devices=1 /dev/sdb2. Разберем ключи приведенной команды:

--create - указывает на то, что мы создаем массив;

--verbose - название массива;

--level=1 - тип RAID-массива, у нас зеркальный;

--force - собирает массив в любом случае, даже при каких-либо несоответствиях, в нашем случае количество дисков в массиве должно быть минимум равно 2;

--raid-devices=1 - задает количество дисков в массиве.

   Удостоверимся в том, что наш массив создан:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image27.jpg)

   Также посмотрим состояние дисков утилитой lsblk:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image28.jpg)

Появился новый RAID-массив, связанный с разделом sdb2, /boot переехал со старого диска(sda) на новый диск(sdb).

После перезагрузки системы название нового RAID-массива изменилось с md63 на md127, также /boot вернулся на sda(перемонтирование выполнилось потом после перенастройки LVM).

![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image29.jpg)

    6.Настраиваем LVM

    Утилита pvs показывает, что в данный момент у нас только один физический том:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image30.jpg)

   Создадим новый, который будет содержать новый массив: pvcreate /dev/md127.

   Посмотрим результат:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image31.jpg)

Заполнилась ячейка FSRYPE напротив md127. "LVM2_member" говорит о том, что данная память предназначена для работы с LVM.

   В выводе pvs появился новый физический том:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image32.jpg)

   Увеличим размер группы system командой vgextend system /dev/md127.

   Размер группы system действительно увеличился(на 7.5g), смотрим выводы утилит vgdisplay, pvs, vgs, lvs:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image35.jpg)
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image36.jpg)

В данный момент локальные тома var, log, root содержатся в массиве md0, на диске sda(ssd3).

    Перенесем логические тома на новый массив командами:

pvmove -i 10 -n /dev/system/root /dev/md0 /dev/md127

pvmove -i 10 -n /dev/system/var /dev/md0 /dev/md127

pvmove -i 10 -n /dev/system/log /dev/md0 /dev/md127

   Получили следующий результат:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image37.jpg)
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image38.jpg)
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image39.jpg)

Физический том md0 освободился, все логические тома переехали на md127.

   Удалим диск из группы командой vgreduce system /dev/md0.

    Смотрим изменения:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image40.jpg)
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image41.jpg)

На физическом томе md0 стала отсутствовать группа, размер группы уменьшился на размер удаленного md0.

    Перемонтируем /boot на новый диск:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image43.jpg)

На sda нет точек монтирования.

    Выполним команду ls /boot:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image44.jpg)

Рассмотрим за что отвечают файлы и каталоги раздела:

grub содержит конфигурационные файлы загрузчика;

vmlinuz - образ ядра linux;

initrd.img - образ этой временной корневой системы, которая используется при начальной загрузке;

system.map - таблица символов, которую использует ядро.

lost+found - каталог, который содержит файлы, имя которых было стерто, но они еще используются системой.

config - файл содержащий установленную конфигурацию ядра.

    7.Удаляем из машины старый диск и добавляем новые.
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image45.jpg)

    8.Утилиты fdisk и lsblk показали, что появились новые доступные для работы диски.
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image46.jpg)
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image47.jpg)

    9.Копируем разделы с ssd4 на ssd5 командой sfdisk -d /dev/sda | sfdisk /dev/sdb. Получаем результат:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image49.jpg)

Можно заметить, что не используется полный объем дисков.

    10.Копируем загрузочный раздел на второй новый диск: dd if=/dev/sda1 of=/dev/sdb1.

    11.Устанавливаем grub на ssd5: grub-install /dev/sdb.

    12.Используем команду fdisk /dev/sdb, с помощью которой:

   +мы удаляем 2 раздел(ключ d);
   +создаем новый раздел(ключ n);
   +указываем на то, что новый раздел является первичным(ключ p) и соглашаемся с вычесленными размерами начала и конца раздела;
   +с помощью ключа l открываем список типов разделов и выбираем тип раздела Linux raid auto;
   +используем ключ t для изменения типа нового раздела;
   +записываем изменения на диск(ключ w)

    13.В ходе выполнения предыдущего пункта мы получили следующий результат:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image50.jpg)

    добавляем к нашему RAID-массиву новый диск и увеличиваем кол-во дисков в массиве:

mdadm —manage /dev/md127 -add /dev/sdb2

mdadm —grow /dev/md127 —raid-devices=2

   Результат:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image51.jpg)

    14.Аналогично пункту 12 увеличиваем размер раздела на ssd4.

    15.Перечитываем таблицу разделов: partx -u /dev/sda и получаем:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image52.jpg)

Размеры разделов sda2 и sdb2 больше чем размер RAID-устройств.

    16.Расширяем raid командами mdadm --grow /dev/md127 --size=max, в выводе lsblk получаем:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image53.jpg)

Размер md127 увеличился.

    17.Мы увеличили размер RAID-массива, но не увеличили размер логических томов.

Увеличиваем размер физического тома:

![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image54.jpg)

Получили 2g свободного места.

    18.Разделяем свободное пространств между томами root и var:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image55.jpg)

Размеры логических томов увеличились. Работа с ssd4 и ssd5 завершена успешно.

    19.Наша новая цель перенести /var/log на новый массив, который состоит из двух новых hdd.

   Узнаем имена новых диков:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image56.jpg)

   Создаем новый RAID-массив из этих дисков и организуем работу LVM(утилиты pvcreate, vgcreate и lvcreate) :
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image57.jpg)

   Создаем том с размером всего массива и оформляем новый раздел в etx4:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image58.jpg)

   Результаты наших действий:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image59.jpg)

    20.Перенос логов со старого раздела на новый:

   Монтируем временное хранилище логов:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image61.jpg)

   С помощью утилиты rsync синхронизируем разделы:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image62.jpg)

   С помощью утилиты для работы со службами(systemctl) останавливаем все процессы, которые работают с /var/log, а с помощью утилиты lsof, которая выводит информацию о том какие файлы используются теми или иными процессами узнаем и проверяем, все ли остановилось:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image63.jpg)

    ыполняем финальную синхронизацию:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image64.jpg)

   Перемонтируем /var/log командами:

umount /mnt
umount /var/log
mount /dev/mapper/data-var_log /var/log

   Результат:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image65.jpg)

    21.Изменяем файл в котором записываются правила, по которым при загрузке будут смонтированы разделы /etc/fstab
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image66.jpg)

Меняем предпоследнюю строчку:

![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image67.jpg)

    22.Используем команду resize2fs для уведомления ФС о изменении размеров разделов:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image69.jpg)

    23.Финальный результат наших действий:
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image70.jpg)
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image71.jpg)
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image72.jpg)

Оба созданных массива функционируют.

    24.После перезагрузки можно загружаться с любого ssd, это никак не сказывается на работоспособности системы.

   Осталось только перераспределить пространство ненужного логического тома(log), для этого используем команду lvremove /dev/system/log, после чего увеличиваем размеры оставшихся томов:
   
![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image73.jpg)

Не забываем уведомить ФС о новых размерах:

![](https://github.com/sashasty/labs-Linux/blob/master/lab2/%20images/image74.jpg)

В ходе выполнения данного задания были получены знания об изменении размера разделов и логических томов, переносе разделов, создании новых RAID-массивов.
