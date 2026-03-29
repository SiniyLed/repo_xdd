## WIREGUARD:
### **!! ВАЖНО** для каждого конфига свой privatekey **!!**

**На DC-RTR-1**
wg genkey | tee /etc/wireguard/privatekey_1dc7 | wg pubkey > /etc/wireguard/publickey_1dc7
wg genkey | tee /etc/wireguard/privatekey_1dc6 | wg pubkey > /etc/wireguard/publickey_1dc6
scp /etc/wireguard/publickey_1dc6 user@88.8.8.27:/home/user
scp /etc/wireguard/publickey_1dc7 user@188.121.90.2:/home/user

**wg0.conf**
```
[Interface]
Address = 10.7.7.1/30
PrivateKey = 
ListenPort = 51820
PostUP = ifmetric %i 10

[Peer]
PublicKey = 
AllowedIPs = 10.7.7.0/24, 224.0.0.0/24
```

**wg1.conf**
```
[Interface]
Address = 10.5.5.1/30
PrivateKey = 
ListenPort = 31820
PostUP = ifmetric %i 20

[Peer]
PublicKey = 
AllowedIPs = 10.5.5.0/24, 224.0.0.0/24
```



**На MSK-RTR**
wg genkey | tee /etc/wireguard/privatekey_msk7 | wg pubkey > /etc/wireguard/publickey_msk7
wg genkey | tee /etc/wireguard/privatekey_msk5 | wg pubkey > /etc/wireguard/publickey_msk5
scp /etc/wireguard/publickey_msk7 user@200.100.100.20:/home/user
scp /etc/wireguard/publickey_msk5 user@100.200.100.20:/home/user

**wg0.conf**
```
[Interface]
Address = 10.7.7.2/30
PrivateKey = 
ListenPort = 51820
PostUP = ifmetric %i 10

[Peer]
PublicKey = 
Endpoint = 200.100.100.20:51820
AllowedIPs = 10.7.7.0/24, 224.0.0.0/24
```

**wg1.conf**
```
[Interface]
Address = 10.5.5.2/30
PrivateKey = 
ListenPort = 31820
PostUP = ifmetric %i 20

[Peer]
PublicKey = 
Endpoint = 100.200.100.20:31820
AllowedIPs = 10.5.5.0/24, 224.0.0.0/24
```


**На DC-RTR-2**
wg genkey | tee /etc/wireguard/privatekey_2dc8 | wg pubkey > /etc/wireguard/publickey_2dc8
wg genkey | tee /etc/wireguard/privatekey_2dc5 | wg pubkey > /etc/wireguard/publickey_2dc5
scp /etc/wireguard/publickey_2dc8 user@88.8.8.27:/home/user
scp /etc/wireguard/publickey_2dc5 user@188.121.90.2:/home/user

**wg1.conf**
```
[Interface]
Address = 10.5.5.1/30
PrivateKey = 
ListenPort = 31820
PostUP = ifmetric %i 20

[Peer]
PublicKey = 
AllowedIPs = 10.5.5.0/24, 224.0.0.0/24
```

**wg0.conf**
```
[Interface]
Address = 10.8.8.1/30
PrivateKey = 
ListenPort = 51820
PostUP = ifmetric %i 10

[Peer]
PublicKey = 
AllowedIPs = 10.8.8.0/24, 224.0.0.0/24
```


**На EKT-RTR**
wg genkey | tee /etc/wireguard/privatekey_ekt8 | wg pubkey > /etc/wireguard/publickey_ekt8
wg genkey | tee /etc/wireguard/privatekey_ekt6 | wg pubkey > /etc/wireguard/publickey_ekt6
scp /etc/wireguard/publickey_msk6 user@200.100.100.20:/home/user
scp /etc/wireguard/publickey_msk8 user@100.200.100.20:/home/user

**wg0.conf**
```
[Interface]
Address = 10.8.8.2/30
PrivateKey = 
ListenPort = 51820
PostUP = ifmetric %i 10

[Peer]
PublicKey = 
Endpoint = 100.200.100.20:51820
AllowedIPs = 10.8.8.0/24, 224.0.0.0/24
```

**wg1.conf**
```
[Interface]
Address = 10.6.6.2/30
PrivateKey = 
ListenPort = 31820
PostUP = ifmetric %i 20

[Peer]
PublicKey = 
Endpoint = 200.100.100.20:31820
AllowedIPs = 10.5.5.0/24, 224.0.0.0/24
```


## В конце:
```
wg-quick up wg0
wg-quick up wg1
```

### **И ОБЯЗАТЕЛЬНО ВЕЗДЕЕ!!!!!**
```
systemctl enable wg-quick@wg1
systemctl enable wg-quick@wg0
```

-----------------------------------------------------------------
## OSPF
```
apt install frr
nano /etc/frr/daemos             ->                     ospfd=yes
systemctl restart frr
systemctl enable --now frr
vtysh
```

**На DC-RTR-1**
```
conf t
router ospf
passive-interface default
exit

int wg0
no ip ospf passive
ip ospf network point-to-point
do wr
exit

int wg1
no ip ospf passive
ip ospf network point-to-point
do wr
exit

router ospf
network 10.7.7.0/24 area 0
network 10.6.6.0/24 area 0
network 10.15.10.0/24 area 0
do wr
exit
```

**На DC-RTR-2**
```
conf t
router ospf
passive-interface default
exit

int wg0
no ip ospf passive
ip ospf network point-to-point
do wr
exit

int wg1
no ip ospf passive
ip ospf network point-to-point
do wr
exit

router ospf
network 10.5.5.0/24 area 0
network 10.8.8.0/24 area 0
network 10.15.10.0/24 area 0
do wr
exit
```

**На MSK-RTR**
```
conf t
router ospf
passive-interface default
exit

int wg0
no ip ospf passive
ip ospf network point-to-point
do wr
exit

int wg1
no ip ospf passive
ip ospf network point-to-point
do wr
exit

router ospf
network 10.5.5.0/24 area 0
network 10.7.7.0/24 area 0
network 192.168.1.0/24 area 0
do wr
exit
```

**На EKT-RTR**
```
conf t
router ospf
passive-interface default
exit

int wg0
no ip ospf passive
ip ospf network point-to-point
do wr
exit

int wg1
no ip ospf passive
ip ospf network point-to-point
do wr
exit

router ospf
network 10.8.8.0/24 area 0
network 10.6.6.0/24 area 0
network 192.168.2.0/24 area 0
do wr
exit
```

Перейдем в режим настройки интерфейса командой
```
interface wg0
```
Включим аутентификацию OSPF командой
```
ip ospf authentication
```
Настроим авторизацию OSPF по ключу DEMO командой
```
ip ospf authentication-key DEMO
```

---------------------------------------------------------------------------
## Уязвимость веб-сервиса
Суть ошибки: на директорию `/var/spool/mail` (которая является ссылкой на `/var/mail`) установлены высокие мандатные метки: **Уровень_3:Низкий**, а также дополнительные категории и флаги.
В Astra Linux 1.8 это означает, что **почтовый сервис или пользователь с нулевым уровнем доступа (обычный вход) не может прочитать содержимое**, так как его уровень допуска ниже, чем у папки с письмами. Именно поэтому сообщения становятся «нечитаемыми».
<img width="1272" height="153" alt="image" src="https://github.com/user-attachments/assets/47a51bcf-244e-4d60-8eb1-9dd17062c302" />


Исправление:
<img width="950" height="251" alt="image" src="https://github.com/user-attachments/assets/e9f414f0-55d2-499e-86a4-5af03ce5194e" />

```
sudo pdpl-file 0:0:0:ccnr /var/mail
```
_Где `0:0:0` — это установка нулевого классификационного уровня, нулевой категории и нулевого уровня целостности._
```
sudo pdpl-file -R 0:0:0:ccnr /var/mail
```
Чтобы изменения применились и к уже существующим письмам внутри папок

------------------------------------------
## Файловый сервер на базе NFS

**На DC-STORAGE:**
1. Устанавливаем пакет nfs-kernel-server
```
apt install nfs-kernel-server -y
```
2. Создаем каталоги
```
mkdir /storage/it
mkdir /storage/office
```
3. Редактируем права доступа
```
chmod -R 1777 /storage/it/
chmod -R 1777 /storage/office/
```
4. Публикуем каталог
```
nano /etc/exports
```
Добавляем необходимые строки
```
/storage/it	*(rw,sync,no_subtree_check)
/storage/office	*(rw,sync,no_subtree_check)
```
Перезагружаем службу nfs-server
```
systemctl restart nfs-server
```
5. Выдаем права доступа к каталогам 
```
chown -R root:1004 /storage/it
chown -R root:1005 /storage/office
```

--------------------------------
## LVM-массив
### !! Важно сперва df -h просмотреть названия дисков !!
1. Устанавливаем пакеты lvm2 cryptsetup
```
apt install lvm2 cryptsetup -y
```
1. Шифруем разделы (ПАРОЛЬ ПУСТОЙ?)
```
cryptsetup luksFormat /dev/sda
cryptsetup luksFormat /dev/sdb
cryptsetup luksFormat /dev/sdc
```
3. Открываем разделы
```
cryptsetup open /dev/sda cryptlvm1
cryptsetup open /dev/sdb cryptlvm2
cryptsetup open /dev/sdc cryptlvm3
```
1. Инициализируем
```
pvcreate /dev/mapper/cryptlvm1
pvcreate /dev/mapper/cryptlvm2
pvcreate /dev/mapper/cryptlvm3
```
5. Объединяем разделы в группу томов
```
vgcreate "Vol2" /dev/mapper/cryptlvm1 /dev/mapper/cryptlvm2  /dev/mapper/cryptlvm3
```
Проверяем так 
```
vgs
pvs
```
6. Собираем логический том
```
lvcreate -l 100%FREE -n crypto_lvm Vol2
```
7. Создаем файловую систему
```
mkfs.ext4 /dev/mapper/Vol2-crypto_lvm
```
8. Создаем каталог
```
mkdir /crypto-folder
```
9. Монтируем вручную для проверки
```
mount /dev/mapper/Vol2-crypto_lvm /crypto-folder
```
10. Генерируем ключ
```
dd if=/dev/urandom of=secretkey bs=512 count=4
```
Копируем его в каталог /etc
```
cp secretkey /etc/
```
11. Добавляем ключ в систему
```
cryptsetup luksAddKey /dev/sda /etc/secretkey
cryptsetup luksAddKey /dev/sdb /etc/secretkey
cryptsetup luksAddKey /dev/sdc /etc/secretkey
```
12. Настраиваем автомонтирование
```
nano /etc/crypttab
```
Приводим его к виду
```
cryptlvm1 /dev/sda /etc/secretkey luks,discard
cryptlvm2 /dev/sdb /etc/secretkey luks,discard
cryptlvm3 /dev/sdc /etc/secretkey luks,discard
```
Добавляем строку в /etc/fstab
```
/dev/mapper/Vol2-crypto_lvm /crypto-folder ext4 defaults 0 0
```
Перезагружаем и проверяем
```
reboot
lsblk
cryptsetup status cryptlvm1
cryptsetup status cryptlvm2
cryptsetup status cryptlvm3
```

<img width="915" height="338" alt="image" src="https://github.com/user-attachments/assets/bfd113a4-ba57-4138-9ce5-4260b71eea0b" />



--------------------------------------------------------------------------
## PAT на пограничных роутерах (внимательно на полученные интерфейсы)

**На MSK-RTR**
<img width="731" height="605" alt="image" src="https://github.com/user-attachments/assets/7c97b8c2-2e13-4656-8431-d98184eef412" />


--------------------------------------------------------------------------
##  DC-RTR-1 и DC-RTR-2 Suricata в режиме IDS









--------------------------------------------------------------------------
## Политики безопасности REMOTE-TERMINAL






-----
## DHCP-сервер на машине MSK-RTR




----
## 





--------------------------------------------------------------------------
## SSH


















