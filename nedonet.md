### Настройка VPN (Wireguard и OpenVPN)
Сначала net.ipv4.ip_forward = 1 \n
На хостах: apt install curl openssl
На роутере: apt-get install -y iptables iproute2 net-tools nginx wireguard openssl

Создание ключа WG: 
wg genkey | tee /etc/wireguard/privatekey | wg pubkey > /etc/wireguard/publickey
cat /etc/wireguard/privatekey

Конфиг WG:
[Interface]
Address = "our ip for tunnel"
PrivateKey = "this host private key"
ListenPort = 

[Peer] #тот_с_кем_коннект
Publickey = ?? #НЕ_НАШ_КЛЮЧ 
AllowedIPs = ??


поднятие сервиса wg (имя wg)
wg-quick up wg 

генерация ключа ssl
openssl genrsa -out /etc/nginx/ssl/nginx.key 2048

генерация самоподписного серта (-nodes без шифрования)
openssl req -x509 -nodes -days 365 \
	-key /etc/nginx/ssl/nginx.key \
	-out etc/nginx/ssl/nginx.crt \
	-subj "/CN=${HOSTNAME}"

конфиг nginx с ssl
<img width="522" height="350" alt="image" src="https://github.com/user-attachments/assets/3548235f-7e2e-4b7f-be13-e7b3b8d1f99b" />


curl -D- -k https://127.0.0.1/

если надо что-то сделать с сертами
cp /etc/nginx/ssl/nginx.crt /usr/local/share/ca-certificates
update-ca-certificates

и дальше curl можно обращаться уже по хостнейму

### OpenVPN
<img width="578" height="253" alt="image" src="https://github.com/user-attachments/assets/6e971124-8872-490b-bc52-1ebfd9c936cc" />



### FreeIPA
создание пользователей
```
#!/bin/bash
# Создаём группы (если их ещё нет)
ipa group-add Administrators --desc "Администраторы"   2>/dev/null || echo "Группа Administrators уже существует"
ipa group-add Worker        --desc "Работники"         2>/dev/null || echo "Группа Worker уже существует"
ipa group-add TopManager    --desc "Топ-менеджеры"     2>/dev/null || echo "Группа TopManager уже существует"

# 1. User1 – User40 → Administrators → пароль P@ssw0rdAdmin
for i in {1..40}; do
    username="user${i}"
    ipa user-add "$username" \
        --first="User${i}" \
        --last="Admin" \
        --password \
        --shell=/bin/bash \
        --homedir="/home/$username"  <<EOF
P@ssw0rdAdmin
P@ssw0rdAdmin
EOF

    ipa group-add-member Administrators --users="$username" 2>/dev/null || true
done

# 2. User41 – User80 → Worker → пароль P@ssw0rdWorker
for i in {41..80}; do
    username="user${i}"
    ipa user-add "$username" \
        --first="User${i}" \
        --last="Worker" \
        --password \
        --shell=/bin/bash \
        --homedir="/home/$username"  <<EOF
P@ssw0rdWorker
P@ssw0rdWorker
EOF

    ipa group-add-member Worker --users="$username" 2>/dev/null || true
done

# 3. SuperUser100 – SuperUser130 → TopManager → пароль P@ssw0rdTop
for i in {100..130}; do
    username="superuser${i}"
    ipa user-add "$username" \
        --first="SuperUser${i}" \
        --last="Top" \
        --password \
        --shell=/bin/bash \
        --homedir="/home/$username"  <<EOF
P@ssw0rdTop
P@ssw0rdTop
EOF

    ipa group-add-member TopManager --users="$username" 2>/dev/null || true
done

echo "Создание завершено."
```

### Журналирование (auditd)
<img width="881" height="228" alt="image" src="https://github.com/user-attachments/assets/a2c6074c-e619-4b05-a7b9-20947815a993" />

### Бэкапы
https://github.com/rda0/ldif-git-backup?ysclid=ml702ojsg5234308302
https://pro-ldap.ru/tr/zytrax/ch8/
