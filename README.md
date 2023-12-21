# In the process of writing...
# Installing FlectraHQ 2.0 with one command (Production).

Installation information and documentation is collected from these sources:

https://github.com/mailcow/mailcow-dockerized

https://docs.mailcow.email/i_u_m/i_u_m_install/

https://docs.mailcow.email/manual-guides/Postfix/u_e-postfix-disable_sender_verification/#deprecated-guide-do-not-use-on-newer-mailcows


## Quick Installation

**Before starting the instance, direct the domain to the ip of the server where Flectra will be installed!**


Run for Ubuntu 22.04

``` bash
sudo apt-get purge needrestart
```

Install docker and docker-compose:

``` bash
curl -s https://raw.githubusercontent.com/6Ministers/fast-mailcow-for-flectrahq/master/setup.sh | sudo bash -s
```

**Установка**

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

В случаи если на вашем сервере памяти меньше чем 2.5GB скрипт предложит вам отключить ClamAV

Необходимо будет указать часовой пояс. Для верного указания используем данную таблицу.

Вы также можете выбрать какую версию сборки mailcow устанавливать, стабильную либо тестовую. Мы будет устанавливать стабильную версию.

``` bash
Available Branches:
- master branch (stable updates) | default, recommended [1]
- nightly branch (unstable updates, testing) | not-production ready [2]
Choose the Branch with it´s number [1/2] 1
```

Сгенерированный файл конфигурации находится в файле mailcow.conf. При необходимости вы можете его отредактировать.

``` bash
nano mailcow.conf
```

Для установки выполняем следующие команды

``` bash
docker compose pull
docker compose up -d
```



## Flectra container management

**Run Flectra**:

``` bash
docker-compose up -d
```

**Restart Flectra**:

``` bash
docker-compose restart
```

**Restart Flectra**:

``` bash
sudo docker-compose down && sudo docker-compose up -d
```

**Stop Flectra**:

``` bash
docker-compose down
```


## docker-compose.yml


