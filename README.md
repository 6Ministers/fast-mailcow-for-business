# Installing FlectraHQ 2.0 with one command (Production).

## Quick Installation

**Before starting the instance, direct the domain to the ip of the server where Flectra will be installed!**

Run for Ubuntu 22.04

``` bash
sudo apt-get purge needrestart
```

Install docker and docker-compose:

``` bash
curl -s https://raw.githubusercontent.com/6Ministers/flectra-2.0prod-docker-compose/master/setup.sh | sudo bash -s
```

**Установка**

Устанавливать mailcow необходимо от пользователя root. Подключаемся к серверу под root пользователем либо выполняем следующую команду

``` bash
sudo su
```

Для установки нам дополнительно потребуется пакет docker-compose-plugin

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

nano mailcow.conf
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

* caddy:alpine
* flectrahq/flectra:2.0
* postgres:14

