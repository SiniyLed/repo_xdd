## WIREGUARD:
### **!! ВАЖНО** для каждого конфига свой privatekey **!!**

**На DC-RTR-1**
```
wg genkey | tee /etc/wireguard/privatekey_1dc7 | wg pubkey > /etc/wireguard/publickey_1dc7
wg genkey | tee /etc/wireguard/privatekey_1dc6 | wg pubkey > /etc/wireguard/publickey_1dc6
scp /etc/wireguard/publickey_1dc6 user@88.8.8.27:/home/user
scp /etc/wireguard/publickey_1dc7 user@188.121.90.2:/home/user
```

**wg0.conf**
```
[Interface]
Address = 10.7.7.1/24
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
Address = 10.6.6.1/24
PrivateKey = 
ListenPort = 31820
PostUP = ifmetric %i 20

[Peer]
PublicKey = 
AllowedIPs = 10.6.6.0/24, 224.0.0.0/24
```



**На MSK-RTR**
```
wg genkey | tee /etc/wireguard/privatekey_msk7 | wg pubkey > /etc/wireguard/publickey_msk7
wg genkey | tee /etc/wireguard/privatekey_msk5 | wg pubkey > /etc/wireguard/publickey_msk5
scp /etc/wireguard/publickey_msk7 user@200.100.100.20:/home/user
scp /etc/wireguard/publickey_msk5 user@100.200.100.20:/home/user
```

**wg0.conf**
```
[Interface]
Address = 10.7.7.2/24
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
Address = 10.5.5.2/24
PrivateKey = 
ListenPort = 31820
PostUP = ifmetric %i 20

[Peer]
PublicKey = 
Endpoint = 100.200.100.20:31820
AllowedIPs = 10.5.5.0/24, 224.0.0.0/24
```


**На DC-RTR-2**
```
wg genkey | tee /etc/wireguard/privatekey_2dc8 | wg pubkey > /etc/wireguard/publickey_2dc8
wg genkey | tee /etc/wireguard/privatekey_2dc5 | wg pubkey > /etc/wireguard/publickey_2dc5
scp /etc/wireguard/publickey_2dc8 user@88.8.8.27:/home/user
scp /etc/wireguard/publickey_2dc5 user@188.121.90.2:/home/user
```

**wg1.conf**
```
[Interface]
Address = 10.5.5.1/24
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
Address = 10.8.8.1/24
PrivateKey = 
ListenPort = 51820
PostUP = ifmetric %i 10

[Peer]
PublicKey = 
AllowedIPs = 10.8.8.0/24, 224.0.0.0/24
```


**На EKT-RTR**
```
wg genkey | tee /etc/wireguard/privatekey_ekt8 | wg pubkey > /etc/wireguard/publickey_ekt8
wg genkey | tee /etc/wireguard/privatekey_ekt6 | wg pubkey > /etc/wireguard/publickey_ekt6
scp /etc/wireguard/publickey_ekt6 user@200.100.100.20:/home/user
scp /etc/wireguard/publickey_ekt8 user@100.200.100.20:/home/user
```

**wg0.conf**
```
[Interface]
Address = 10.8.8.2/24
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
Address = 10.6.6.2/24
PrivateKey = 
ListenPort = 31820
PostUP = ifmetric %i 20

[Peer]
PublicKey = 
Endpoint = 200.100.100.20:31820
AllowedIPs = 10.6.6.0/24, 224.0.0.0/24
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
\
<img width="731" height="605" alt="image" src="https://github.com/user-attachments/assets/7c97b8c2-2e13-4656-8431-d98184eef412" />


--------------------------------------------------------------------------
##  DC-RTR-1 и DC-RTR-2 Suricata в режиме IDS

1. Установка
```
apt update && apt install suricata -y
```
2. Интерфейсы
```
Узнайте имя интерфейса, смотрящего на провайдера (ISP), через `ip a`. Допустим, это `eth0`. Отредактируйте `/etc/suricata/suricata.yaml`:

- Найдите секцию `af-packet:` и в поле `interface:` укажите `eth0`.
- Убедитесь, что в секции `outputs:` включен `eve-log` с типом `filetype: regular` (формат JSON по умолчанию).
```
```
outputs:
  - eve-log:
      enabled: yes
      filetype: regular
      filename: eve.json
      types:
        - alert
        - http
        - dns
        - tls
```

3. Создание правил обнаружения
Создайте файл `/etc/suricata/rules/local.rules` и добавьте правила:
```
# Детект сканирования (более 15 попыток соединения за 10 сек)
alert tcp $EXTERNAL_NET any -> $HOME_NET any (msg:"SCAN Portscan Detected"; flags:S; threshold: type both, track by_src, count 15, seconds 10; sid:1000001; rev:1;)

# Детект брутфорса SSH (5 попыток за 60 сек)
alert tcp $EXTERNAL_NET any -> $HOME_NET 22 (msg:"ATTACK SSH Brute-force"; flow:to_server; flags:S,12; threshold: type both, track by_src, count 5, seconds 60; sid:1000002; rev:1;)
```

Включите файл в конфиг `suricata.yaml` (секция `rule-files`) и перезапустите службу: `systemctl restart suricata`.

#### Настройка агентов Wazuh (DC-RTR и устройства ЕКБ)
1. Установка
```
apt update && WAZUH_MANAGER=192.168.2.200 apt install wazuh-agent -y
```
**На DC-RTR-1 и DC-RTR-2 (дополнительно):**  

Чтобы агент забирал логи Suricata, в `/var/ossec/etc/ossec.conf` добавьте:

```
<localfile>
  <log_format>json</log_format>
  <location>/var/log/suricata/eve.json</location>
</localfile>
```

`systemctl restart wazuh-agent`

#### Настройка сервера YEKT-WORKER (Wazuh Manager)

Все настройки делаются в `/var/ossec/etc/rules/local_rules.xml`.

1. Группировка роутеров:  
Сначала объедините логи Suricata с роутеров.
```
<group name="suricata,routers,">
  <rule id="100001" level="3">
    <decoded_as>json</decoded_as>
    <location>/var/log/suricata/eve.json</location>
    <description>Suricata events collection</description>
  </rule>
</group>
```
2. Правило корреляции: Брутфорс SSH (5 входов за 5 минут):

Wazuh уже видит неудачные входы (ID 5710). Создадим правило, которое "выстрелит" только при частом повторении:
```
<rule id="100002" level="10" frequency="5" timeframe="300">
  <if_matched_sid>5710</if_matched_sid>
  <same_source_ip />
  <description>Multiple SSH authentication failures (5 in 5 min) from same IP.</description>
</rule>
```

3. Мониторинг конфигов (FIM):

Чтобы Wazuh следил за файлами, на сервере (в секции `<syscheck>` конфига `ossec.conf`) или через группы агентов добавьте:

```
<syscheck>
  <directories check_all="yes" report_changes="yes" realtime="yes">/etc/passwd,/etc/shadow</directories>
  <directories check_all="yes" report_changes="yes" realtime="yes">/etc/ssh/sshd_config</directories>
  <directories check_all="yes" report_changes="yes" realtime="yes">/etc/ssh/sshd_config.d/*.conf</directories>
</syscheck>
```

#### Привязка к группе routers

Чтобы роутеры считались группой `routers`, выполните на сервере:

1. Создайте группу: `/var/ossec/bin/agent_groups -a -g routers`
2. Добавьте в неё агентов: `/var/ossec/bin/agent_groups -a -i [ID_АГЕНТА] -g routers` (ID можно узнать через `agent_control -l`).

**Проверить поступление логов** можно в веб-интерфейсе Wazuh во вкладке **Security Events**, отфильтровав по `rule.groups: suricata`.


--------------------------------------------------------------------------
## Политики безопасности REMOTE-TERMINAL

1. Ограничение интерпретаторов
```
chmod 700 /usr/bin/python* /usr/bin/perl /usr/bin/php* /usr/bin/ruby* 
```

2. Скрипт отправки логов через rsync
Установка sshpass
```
apt install sshpass
```
Создаем скрипт `/usr/local/bin/send_logs.sh`
```
#!/bin/bash
TIMESTAMP=$(date +%H%M)
LOG_NAME="auth.log-$TIMESTAMP"

export RSYNC_PASSWORD='RsynC_PA$$'
rsync -avz /var/log/auth.log rsync_user@88.8.8.27::www_effort_cool/$LOG_NAME

```
3. Настройка планировщика (Cron)
```
echo "0 * * * * root /usr/local/bin/send_logs.sh" | tee -a /etc/crontab
systemctl restart cron
```
4. Настройка стороны приемника (YEKT-RTR)
На стороне сервера в файле `/etc/rsyncd.conf` должен быть описан модуль:
```
[www_effort_cool]
    path = /var/www/effort.cool/logs/
    read only = no
    auth users = rsync_user
    secrets file = /etc/rsyncd.secrets
```
В файл `/etc/rsyncd.secrets` пишем `rsync_user:RsynC_PA$$`
Установка владельца 
```
chown root:root /etc/rsyncd.secrets
```
Установка прав 600
```
chmod 600 /etc/rsyncd.secrets
```

-----
## DHCP-сервер на машине MSK-RTR

1. Установка
```
apt update && apt install isc-dhcp-server -y
```
2. Отредактировать файл `/etc/default/isc-dhcp-server`, чтобы указать интерфейс, на котором будет работать сервер (например, `eth1` или `enp0s8` — уточнить через `ip a`):
```
INTERFACESv4="ваш_интерфейс"
```
3. Конфигурация пула
Отредактируйте основной файл конфигурации `/etc/dhcp/dhcpd.conf`. Приведите его к следующему виду (лишнее можно закомментировать):
```
# Опции домена и DNS
option domain-name "effort.cool";
option domain-name-servers 192.168.1.2, 77.88.8.1;

default-lease-time 600;
max-lease-time 7200;

# Описание подсети
subnet 192.168.1.0 netmask 255.255.255.0 {
  range 192.168.1.50 192.168.1.100;
  option routers 192.168.1.1; # Укажите IP этого роутера (MSK-RTR)
  option subnet-mask 255.255.255.0;
  option broadcast-address 192.168.1.255;
}
```
4. Перезапуск и автозапуск
```
systemctl restart isc-dhcp-server
systemctl enable isc-dhcp-server
```
### На клиенте
Через `nano /etc/network/interfaces` пишем конфиг
```
    auto eth0
    allow-hotplug eth0
    iface eth0 inet dhcp
```


--------------------------------------------------------------------------
## SSH

1. Создание пользователя
```
useradd -m -s /bin/bash cod_admin
echo "cod_admin ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/cod_admin
chmod 0440 /etc/sudoers.d/cod_admin
```
2. Файл `/etc/ssh/sshd_config.d/atom.conf`
```
# Заменить IP_LOCAL на конкретный адрес машины в этих сетях
# ListenAddress 10.x.x.x
# ListenAddress 192.168.x.x

Port 22
PermitRootLogin no
MaxStartups 3
ClientAliveInterval 300
ClientAliveCountMax 0

# Авторизация только по ключам
PasswordAuthentication no
PubkeyAuthentication yes
AuthorizedKeysFile /ssh_keys/cod_admin.pub
```

#### На DC-STORAGE
Установка NFS
```
apt install nfs-kernel-server -y
```
Папка с публичным ключом
```
mkdir -p /ssh_keys
# Скопируйте сюда ваш id_rsa.pub и назовите его cod_admin.pub
chmod 755 /ssh_keys
chmod 644 /ssh_keys/cod_admin.pub
```
Разрешаем доступ
```
# Разрешаем сетям 10.0.0.0/8 и 192.168.0.0/16 чтение (ro)
/ssh_keys 10.0.0.0/8(ro,sync,no_subtree_check) 192.168.0.0/16(ro,sync,no_subtree_check)
```
Применяем настройки `exportfs -ra`

#### На целевых машинах
Установка клиента `apt install nfs-common -y`

Создание пустой папки-точки монтирования: `mkdir /ssh_keys`

Добавление записи в `/etc/fstab`, чтобы папка подключалась автоматически при загрузке:
```
10.15.10.150:/ssh_keys /ssh_keys nfs ro,soft,intr 0 0
```
Примонтирвать прямо сейчас: `mount -a`




















