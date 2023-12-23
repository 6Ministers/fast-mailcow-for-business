# Русская инструкция находится после английской
# Fast installing MailCow for FlectraHQ (Production).

- Preparation and installation takes 10 minutes (points 1-4)
- Adding a mail domain and configuring it - 15 minutes (points 5-7)
- Settings test - 5 minutes (point 8)
- FlectraHQ connection - 5 minutes (point 9)
---

1. [MailCow Server Requirements](https://github.com/6Ministers/fast-mailcow-for-flectrahq/blob/master/README.md#1-требования-к-серверу)
2. [Preparing the MailCow DNS server]()
3. [Preparing the MailCow DNS server]()
4. [Quick installation of MailCow]()
5. [Log in and change the administrator password]()
6. [Adding a mail domain]()
7. [Configuring the DNS of the mail domain]()
8. [Mail Domain Test]()
9. [FlectraHQ Connection]()
10. [Working with MailCow]()
11. [Commands to work with]()
12. [The reputation of the mail domain]()
13. [SOGo Web Mail Client]()
14. [Spam protection]()
15. [Links to documentation]()

## 1. MailCow Server Requirements
От и более
- 2 CPUs
- 2 RAM (без [full-text search in Dovecot](https://docs.mailcow.email/manual-guides/Dovecot/u_e-dovecot-fts/))
- 15 Gb (для установки хватит и на первое время хватит)

## 2. Preparing the MailCow DNS server
Send in DNS the subdomain to the mail server MailCow

**DNS**

``` bash
# Name              Type       Value
mail                IN A       IP MailCow
@                   IN MX 10   mail.mailcow.ltd. (your ${MAILCOW_HOSTNAME})
@                   IN MX 20   mail.mailcow.ltd. (your ${MAILCOW_HOSTNAME})
```

Documentation
https://docs.mailcow.email/prerequisite/prerequisite-dns/


## 3. Preparing the MailCow DNS server

Run for Ubuntu 22.04

``` bash
sudo apt-get purge needrestart
```

Install docker and docker-compose:

``` bash
curl -s https://raw.githubusercontent.com/6Ministers/fast-mailcow-for-flectrahq/master/setup.sh | sudo bash -s
```

## 4. Quick installation of MailCow


MailCow must be installed from the root user. Connect to the server under the root user or run the following command

``` bash
sudo su
```

To install, we will additionally need a package 

``` bash
apt install docker-compose-plugin
```

apt install docker-compose-plugin
Go to the opt folder and clone the MailCow repository and go to the `mailbox-dockerized folder`

``` bash
cd /opt
git clone https://github.com/mailcow/mailcow-dockerized
cd mailcow-dockerized
```

**Starting the generation of the configuration file**

``` bash
./generate_config.sh
```

During the generation of the configuration file, we will be asked for the domain name on which the mailcow system will be located, we advise you to create a subdomain for mail on your domain and specify it.

Specify the mail subdomain 

``` bash
mail.domain.ltd
```

If your server has less than 2.5 GB of memory, the script will prompt you to disable ClamAV
``` bash
Y
```
You will need to specify the time zone. For the correct indication, we use this table.

We will specify the time zone

https://en.wikipedia.org/wiki/List_of_tz_database_time_zones

You can also choose which version of the mailcow build to install, stable or test. We will install the stable version.

``` bash
Available Branches:
- master branch (stable updates) | default, recommended [1]
- nightly branch (unstable updates, testing) | not-production ready [2]
Choose the Branch with it´s number [1/2] 1
```

The generated configuration file is located in the file `mailcow.conf`. If necessary, you can edit it.

``` bash
nano mailcow.conf
```

Located in the catalog `/opt/mailcow-dockerized`

**Installationа**
To install, run the following commands

``` bash
docker compose pull
docker compose up -d
```

## 5. Log in and change the administrator password

`https://mail.mailcow.ltd/`

![login](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/0d1f8c64-da87-4185-814c-4540eb6a1e00)


**login:admin**

**Password:moohoo**

![dashboard](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/9aafe11f-bd4f-4377-a95a-734cc7916901)


## 6. Adding a mail domain

![add](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/3c0b256f-9666-4171-948c-896f978c32a6)

![domain](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/5a86b719-7475-493b-9c72-d28e82fa88ea)

![add-domain](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/6bb587d4-d803-4d0b-9b01-6ae12d308ac9)


## 7. Configuring the DNS of the mail domain

For Gmail com to work, you need to add the following DNS records for your domain
``` bash
# Name              Type       Value
domaim.ltd          IN A       1.2.3.4
@                   IN MX 10   mail.mailcow.ltd. (your ${MAILCOW_HOSTNAME})
@                   IN MX 20   mail.mailcow.ltd. (your ${MAILCOW_HOSTNAME})
autodiscover        IN CNAME   mail.mailcow.ltd. (your ${MAILCOW_HOSTNAME})
autoconfig          IN CNAME   mail.mailcow.ltd. (your ${MAILCOW_HOSTNAME})
```

**SPF**
You also need to add an SPF record specifying the IP of your server in it

``` bash
@                   IN TXT     "v=spf1 mx a SERVER_IP -all"
```

**DKIM**
And specify the DKIM entry that is available in the Mailgov control panel in the section
Configuration — Configuration & Details — ARC/DKIM keys

``` bash
dkim._domainkey     IN TXT     "v=DKIM1; k=rsa; t=s; s=email; p=..."
```

**DMARC**
``` bash
# Name              Type       Value
_dmarc              IN TXT     "v=DMARC1; p=reject; rua=mailto:mailauth-reports@example.org"
```

**Advanced Settings**

https://docs.mailcow.email/prerequisite/prerequisite-dns/#the-advanced-dns-configuration

``` bash
# Name              Type       Priority Weight Port    Value
_autodiscover._tcp  IN SRV     0        1      443      mail.example.org. (your ${MAILCOW_HOSTNAME})
_caldavs._tcp       IN SRV     0        1      443      mail.example.org. (your ${MAILCOW_HOSTNAME})
_caldavs._tcp       IN TXT                              "path=/SOGo/dav/"
_carddavs._tcp      IN SRV     0        1      443      mail.example.org. (your ${MAILCOW_HOSTNAME})
_carddavs._tcp      IN TXT                              "path=/SOGo/dav/"
_imap._tcp          IN SRV     0        1      143      mail.example.org. (your ${MAILCOW_HOSTNAME})
_imaps._tcp         IN SRV     0        1      993      mail.example.org. (your ${MAILCOW_HOSTNAME})
_pop3._tcp          IN SRV     0        1      110      mail.example.org. (your ${MAILCOW_HOSTNAME})
_pop3s._tcp         IN SRV     0        1      995      mail.example.org. (your ${MAILCOW_HOSTNAME})
_sieve._tcp         IN SRV     0        1      4190     mail.example.org. (your ${MAILCOW_HOSTNAME})
_smtps._tcp         IN SRV     0        1      465      mail.example.org. (your ${MAILCOW_HOSTNAME})
_submission._tcp    IN SRV     0        1      587      mail.example.org. (your ${MAILCOW_HOSTNAME})
```

Documentation:

https://docs.mailcow.email/prerequisite/prerequisite-dns/

## 8. Mail Domain Test

Here are some tools you can use to verify your DNS configuration:

- [MX Toolbox (DNS, SMTP, RBL)](https://mxtoolbox.com/SuperTool.aspx)
- [port25.com (DKIM, SPF)](https://www.port25.com/dkim-wizard/)
- [Mail-tester (DKIM, DMARC, SPF)](https://www.mail-tester.com/)
- [DMARC Analyzer (DMARC, SPF)](https://www.dmarcanalyzer.com/spf/checker/)
- [MultiRBL.valli.org (DNSBL, RBL, FCrDNS)](http://multirbl.valli.org/)


## 9. FlectraHQ Connection

In order for emails to be sent from any domain, you need to change the settings in MailCow
Disable Sender Addresses Verification

https://docs.mailcow.email/manual-guides/Postfix/u_e-postfix-disable_sender_verification/#deprecated-guide-do-not-use-on-newer-mailcows

![mailbox](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/90076104-f054-4660-b756-54d5b623c963)

![send-as](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/d10a896a-a3d3-4cc8-a2ab-b041da19af39)

![flectra-outgoing](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/a01c12f7-6ca5-4e5a-ac46-044e45eaa1b7)

![flectra-incoming](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/20963c67-19aa-4aa9-955e-edefa3f94b14)


## 10. Working with MailCow

**Installation**

https://docs.mailcow.email/i_u_m/i_u_m_install/


**Update**

An update script in your mailcow-dockerized directory will take care of updates.

Run the update script:

``` bash
./update.sh
```
Documentation:
https://docs.mailcow.email/i_u_m/i_u_m_update/?h=update.sh

**Migration**

https://docs.mailcow.email/i_u_m/i_u_m_migration/


**Deinstallation**

https://docs.mailcow.email/i_u_m/i_u_m_deinstall/


## 11. Commands to work with

**MailCow container management**

**Run MailCow**:

``` bash
docker-compose up -d
```

**Restart MailCow**:

``` bash
docker-compose restart
```

**Stop MailCow**:

``` bash
docker-compose down
```

**Restart MailCow**:

``` bash
sudo docker-compose down && sudo docker-compose up -d
```

## 12. The reputation of the mail domain

- [Postmaster Tool](https://gmail.com/postmaster)
- [parsedmarc (self-hosted)](https://github.com/domainaware/parsedmarc)
- [Fraudmarc](https://fraudmarc.com/)
- [Postmark](https://dmarc.postmarkapp.com/)
- [Dmarcian](https://dmarcian.com/)


## 13. SOGo Web Mail Client

After creating a mail account, the mail client is located at:

`https://mail.mailcow.ltd/SOGo/`

![Webmail-Sogo](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/ada04f21-c07c-4a68-b911-38fe69061b0f)

Log in to the web client using the username and password of the email account:
![Sogo](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/919bfea9-4de4-4583-8d91-e19ba7c801a6)


## 14. Spam protection

В MailCow интегрирован Rspamd - Fast, free and open-source spam filtering system.
https://rspamd.com/

## 15. Links to documentation

Installation information and documentation is collected from these sources:

https://docs.mailcow.email/

https://github.com/mailcow/mailcow-dockerized

https://docs.mailcow.email/i_u_m/i_u_m_install/

https://docs.mailcow.email/i_u_m/i_u_m_update/?h=update.sh

https://docs.mailcow.email/manual-guides/Postfix/u_e-postfix-disable_sender_verification/#deprecated-guide-do-not-use-on-newer-mailcows


**Rus**

https://habr.com/ru/articles/343128/

-------
# Русская инструкция

# In the process of writing...
# Fast installing MailCow for FlectraHQ (Production).

- Подготовка и установка занимает - 10 минут (пункты 1-4)
- Добавление почтового домена и его настройка - 15 минут (пункты 5-7)
- Тест настроек - 5 минут (пункт 8)
- Подключение FlectraHQ - 5 минут (пункт 9)
---

1. Требования к серверу MailCow
2. Подготовка DNS сервера MailCow
3. Подготовка сервера к установке MailCow
4. Быстрая установка MailCow
5. Вход и смена пароля администратора
6. Добавление почтового домена
7. Настройка DNS почтового домена
8. Тест почтового домена
9. Подключение FlectraHQ
10. Работа с MailCow
11. Команды для работы 
12. Репутация почтового домена
13. Web почтовый клиент SOGo
14. Защита от спама
15. Ссылки на документацию



## 1. Требования к серверу
От и более
- 2 CPUs
- 2 RAM (без [full-text search in Dovecot](https://docs.mailcow.email/manual-guides/Dovecot/u_e-dovecot-fts/))
- 15 Gb (для установки хватит и на первое время хватит)

## 2. Подготовка DNS сервера MailCow
Send in DNS the subdomain to the mail server MailCow

**DNS**

``` bash
# Name              Type       Value
mail                IN A       IP MailCow
@                   IN MX 10   mail.mailcow.ltd. (your ${MAILCOW_HOSTNAME})
@                   IN MX 20   mail.mailcow.ltd. (your ${MAILCOW_HOSTNAME})
```

Документация
https://docs.mailcow.email/prerequisite/prerequisite-dns/


## 3. Подготовка сервера к установке MailCow

Run for Ubuntu 22.04

``` bash
sudo apt-get purge needrestart
```

Install docker and docker-compose:

``` bash
curl -s https://raw.githubusercontent.com/6Ministers/fast-mailcow-for-flectrahq/master/setup.sh | sudo bash -s
```

## 4. Quick Installation MailCow


Устанавливать mailcow необходимо от пользователя root. Подключаемся к серверу под root пользователем либо выполняем следующую команду

``` bash
sudo su
```

Для установки нам дополнительно потребуется пакет 

``` bash
apt install docker-compose-plugin
```

apt install docker-compose-plugin
Переходим в папку opt и клонируем репозиторий mailcow и переходим в папку mailcow-dockerized

``` bash
cd /opt
git clone https://github.com/mailcow/mailcow-dockerized
cd mailcow-dockerized
```

**Запускаем генерацию файла конфигурации**

``` bash
./generate_config.sh
```

Во время генерации конфигурационного файла у нас будет запрошено доменное имя на котором будет находится система mailcow, советуем вам создать на вашем домене поддомен для почты и указать его.

Specify the mail subdomain 

``` bash
mail.domain.ltd
```

В случаи если на вашем сервере памяти меньше чем 2.5GB скрипт предложит вам отключить ClamAV
``` bash
Y
```
Необходимо будет указать часовой пояс. Для верного указания используем данную таблицу.

We will specify the time zone

https://en.wikipedia.org/wiki/List_of_tz_database_time_zones

Вы также можете выбрать какую версию сборки mailcow устанавливать, стабильную либо тестовую. Мы будет устанавливать стабильную версию.

``` bash
Available Branches:
- master branch (stable updates) | default, recommended [1]
- nightly branch (unstable updates, testing) | not-production ready [2]
Choose the Branch with it´s number [1/2] 1
```

Сгенерированный файл конфигурации находится в файле `mailcow.conf`. При необходимости вы можете его отредактировать.

``` bash
nano mailcow.conf
```

Находится в каталоге `/opt/mailcow-dockerized`

**Установка**
Для установки выполняем следующие команды

``` bash
docker compose pull
docker compose up -d
```

## 5. Вход и смена пароля администратора

`https://mail.mailcow.ltd/`

![login](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/0d1f8c64-da87-4185-814c-4540eb6a1e00)


**login:admin**

**Password:moohoo**

![dashboard](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/9aafe11f-bd4f-4377-a95a-734cc7916901)


## 6. Добавление почтового домена

![add](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/3c0b256f-9666-4171-948c-896f978c32a6)

![domain](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/5a86b719-7475-493b-9c72-d28e82fa88ea)

![add-domain](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/6bb587d4-d803-4d0b-9b01-6ae12d308ac9)


## 7. Настройка DNS почтового домена

Для работ Mailcow вам необходимо добавить следующие DNS записи для вашего домена
``` bash
# Name              Type       Value
domaim.ltd          IN A       1.2.3.4
@                   IN MX 10   mail.mailcow.ltd. (your ${MAILCOW_HOSTNAME})
@                   IN MX 20   mail.mailcow.ltd. (your ${MAILCOW_HOSTNAME})
autodiscover        IN CNAME   mail.mailcow.ltd. (your ${MAILCOW_HOSTNAME})
autoconfig          IN CNAME   mail.mailcow.ltd. (your ${MAILCOW_HOSTNAME})
```

**SPF**
Так же необходимо добавить SPF запись указав в ней IP вашего сервера

``` bash
@                   IN TXT     "v=spf1 mx a SERVER_IP -all"
```

**DKIM**
И указать DKIM запись которая доступна в панели управления Mailcow в разделе Configuration — Configuration & Details — ARC/DKIM keys

``` bash
dkim._domainkey     IN TXT     "v=DKIM1; k=rsa; t=s; s=email; p=..."
```

**DMARC**
``` bash
# Name              Type       Value
_dmarc              IN TXT     "v=DMARC1; p=reject; rua=mailto:mailauth-reports@example.org"
```

Расширенные настройки
https://docs.mailcow.email/prerequisite/prerequisite-dns/#the-advanced-dns-configuration

``` bash
# Name              Type       Priority Weight Port    Value
_autodiscover._tcp  IN SRV     0        1      443      mail.example.org. (your ${MAILCOW_HOSTNAME})
_caldavs._tcp       IN SRV     0        1      443      mail.example.org. (your ${MAILCOW_HOSTNAME})
_caldavs._tcp       IN TXT                              "path=/SOGo/dav/"
_carddavs._tcp      IN SRV     0        1      443      mail.example.org. (your ${MAILCOW_HOSTNAME})
_carddavs._tcp      IN TXT                              "path=/SOGo/dav/"
_imap._tcp          IN SRV     0        1      143      mail.example.org. (your ${MAILCOW_HOSTNAME})
_imaps._tcp         IN SRV     0        1      993      mail.example.org. (your ${MAILCOW_HOSTNAME})
_pop3._tcp          IN SRV     0        1      110      mail.example.org. (your ${MAILCOW_HOSTNAME})
_pop3s._tcp         IN SRV     0        1      995      mail.example.org. (your ${MAILCOW_HOSTNAME})
_sieve._tcp         IN SRV     0        1      4190     mail.example.org. (your ${MAILCOW_HOSTNAME})
_smtps._tcp         IN SRV     0        1      465      mail.example.org. (your ${MAILCOW_HOSTNAME})
_submission._tcp    IN SRV     0        1      587      mail.example.org. (your ${MAILCOW_HOSTNAME})
```

Документация:

https://docs.mailcow.email/prerequisite/prerequisite-dns/

## 8. Тест почтового домена

Here are some tools you can use to verify your DNS configuration:

- [MX Toolbox (DNS, SMTP, RBL)](https://mxtoolbox.com/SuperTool.aspx)
- [port25.com (DKIM, SPF)](https://www.port25.com/dkim-wizard/)
- [Mail-tester (DKIM, DMARC, SPF)](https://www.mail-tester.com/)
- [DMARC Analyzer (DMARC, SPF)](https://www.dmarcanalyzer.com/spf/checker/)
- [MultiRBL.valli.org (DNSBL, RBL, FCrDNS)](http://multirbl.valli.org/)


## 9. Подключение FlectraHQ

Чтобы письма могли отправлятся от любого домена, нужно изменить настройки в MailCow
Disable Sender Addresses Verification

https://docs.mailcow.email/manual-guides/Postfix/u_e-postfix-disable_sender_verification/#deprecated-guide-do-not-use-on-newer-mailcows

![mailbox](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/90076104-f054-4660-b756-54d5b623c963)

![send-as](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/d10a896a-a3d3-4cc8-a2ab-b041da19af39)

![flectra-outgoing](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/a01c12f7-6ca5-4e5a-ac46-044e45eaa1b7)

![flectra-incoming](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/20963c67-19aa-4aa9-955e-edefa3f94b14)

## 10. Работа с MailCow

**Installation**

https://docs.mailcow.email/i_u_m/i_u_m_install/


**Update**

An update script in your mailcow-dockerized directory will take care of updates.

Run the update script:

``` bash
./update.sh
```
Документация:
https://docs.mailcow.email/i_u_m/i_u_m_update/?h=update.sh

**Migration**

https://docs.mailcow.email/i_u_m/i_u_m_migration/


**Deinstallation**

https://docs.mailcow.email/i_u_m/i_u_m_deinstall/


## 11. Команды для работы 

**MailCow container management**

**Run MailCow**:

``` bash
docker-compose up -d
```

**Restart MailCow**:

``` bash
docker-compose restart
```

**Stop MailCow**:

``` bash
docker-compose down
```

**Restart MailCow**:

``` bash
sudo docker-compose down && sudo docker-compose up -d
```

## 12. Репутация почтового домена

- [Postmaster Tool](https://gmail.com/postmaster)
- [parsedmarc (self-hosted)](https://github.com/domainaware/parsedmarc)
- [Fraudmarc](https://fraudmarc.com/)
- [Postmark](https://dmarc.postmarkapp.com/)
- [Dmarcian](https://dmarcian.com/)


## 13. Web почтовый клиент SOGo

После создание почтового аккаунта почтовый клиент нахощдится по адресу:
`https://mail.mailcow.ltd/SOGo/`

![Webmail-Sogo](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/ada04f21-c07c-4a68-b911-38fe69061b0f)

Вход в веб клиент по логину и паролю почтового аккаунта:
![Sogo](https://github.com/6Ministers/fast-mailcow-for-flectrahq/assets/11208423/919bfea9-4de4-4583-8d91-e19ba7c801a6)


## 14. Защита от спама

В MailCow интегрирован Rspamd - Fast, free and open-source spam filtering system.
https://rspamd.com/

## 15. Ссылки на документацию

Installation information and documentation is collected from these sources:

https://docs.mailcow.email/

https://github.com/mailcow/mailcow-dockerized

https://docs.mailcow.email/i_u_m/i_u_m_install/

https://docs.mailcow.email/i_u_m/i_u_m_update/?h=update.sh

https://docs.mailcow.email/manual-guides/Postfix/u_e-postfix-disable_sender_verification/#deprecated-guide-do-not-use-on-newer-mailcows


**Rus**

https://habr.com/ru/articles/343128/


