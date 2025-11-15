[Mutt](https://en.wikipedia.org/wiki/Mutt_(email_client)) текстовый почтовый клиент. Позволяет принимать (POP3, IMAP и т.д.) просматривать почтовые сообщения. По умолчании самостоятельно не может отправлять почту, полагаясь в этом на [sendmail](https://en.wikipedia.org/wiki/Sendmail). В отдельной заметке скомпилируем mutt с ключом _--enable-smtp_ и попробуем его отправлять почту самостоятельно.  
[msmtp](https://en.wikipedia.org/wiki/Msmtp) - smtp клиент. Его будем использовать для отправки почты.  

Устанавливаем необходимый софт:  
````bash
apt install mutt msmtp
````  

Создаем необходимые папки и файлы конфигурации  
```bash
mkdir ~/.mutt &&  touch ~/.mutt/muttrc ~/.mutt/set touch /etc/msmtprc
chmod go-rwx /etc/msmtprc
````
Отредактируем файл /etc/msmtprc. В данном конфиге будет использоваться почта Yandex.ru
````bash
nano -b /etc/msmtprc
````
содержимое:
````
defaults
logfile /var/log/msmtp
tls on
tls_starttls on

account localhost
host localhost
auto_from on

account yandex
host smtp.yandex.ru
port 587
from {{username}}@yandex.ru
user {{username}}
password {{password}}
auth on
tls on
tls_starttls on 
tls_certcheck off

account default : yandex
````
После правки конфига и корректной настройки самого клиента в веб интерфейсе yandex.ru (ссылка на статью) появляется возможность отправлять письма.  
````bash
# sending mail form terminal

printf "Subject: Test\n\nHello there username." | msmtp -a default someuser@gmail.com
echo -e "Subject: Test\n\nHello! Mail form `hostname`" | msmtp -d someuser@gmail.com
echo "Subject: Тема письма "$'\n\n' | msmtp -F `hostname` -d usera@example.com
echo "Subject: На 2 адресата "$'\n\n' | msmtp -F `hostname` -d -- usera@example.com userb@example.com

# sending email by using composed mail file
# create file
touch /usr/local/etc/mail_sample.mail
# add comnent
To: usera@example.com
From: user00@yandex.ru
Subject: A test
# and use
cat /usr/local/etc/mail_sample.mail | msmtp -a default sunika@gmail.com
````
Примечание, в случае получения сообщения:
````bash
msmtp: server message: 554 5.7.1 Message rejected under suspicion of SPAM; https://ya.cc/1IrBc 1762776177-v2MF7s0LwGk0-zNne4zMr 
````
достаточно отправить письмо через родной веб-интерфейс Яндекс-а на этот адрес.
Для отправки Простых сообщений можнос использовать msmtp, но если надо допустим отправить письмо с вложением тут нужно сформировать письмо 
по стандарту [MIME](https://en.wikipedia.org/wiki/MIME), чего msmtp не может. Для этого будем использовать mutt.  

Отредактируем файл ~/.mutt/muttrc
````bash
nano -b ~/.mutt/muttrc
````
содержимое:
````
source ~/.mutt/set
````  
Затем ~/.mutt/set  

```` bash
nano -b ~/.mutt/set
````  
Содержимое:  
```
set sendmail="/usr/bin/msmtp"
set use_from=yes
set from="{{user}}@yandex.ru"
```
Теперь можно отправлять письма:  

````bash
echo "Текст письма" | mutt -s `hostname`  user@example.com
echo "Текст письма" | mutt -e 'set realname=`hostname`' -s `hostname`  user@example.com
````


---










