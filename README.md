# Задание 1

"# zabbix" 

[skreen](https://github.com/Aid1986/zabbix/blob/main/авторизация%202024-04-14%20163628.png

Zabbix 6.0 на Ubuntu Server 22.04.3

Ресурсы:
Оперативная память - 8 Gb
Потоков процессора - 4
Объема жесткого диска - 64 Gb

------------------------------------------------------------------------------------------ШАГ 1--------------------------------------------------------

apt install chrony
timedatectl set-timezone Europe/Moscow
systemctl enable chrony
systemctl status chrony

apt install mc
apt install vim
apt install wget
apt install net-tools

apt install locales
dpkg-reconfigure locales						# выбрать через пробел ru_RU.UTF-8 UTF-8

sudo apt update

reboot

------------------------------------------------------------------------------------------ШАГ 2---------------------------------------------------------

https://www.postgresql.org/download/linux/ubuntu/

sudo sh -c 'echo "deb https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'	# создание конфигурации для файлового хранилища
cat /etc/apt/sources.list.d/pgdg.list													# проверка
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -						# импорт ключа подписи репозитория
sudo apt-get update															# пересасываем apt-хэш
sudo apt install postgresql-14														# установка PostgreSQL
systemctl status postgresql
netstat -tulpn																# прослушка PostgreSQL работает через порт :5432

------------------------------------------------------------------------------------------ШАГ 3--------------------------------------------------------

https://www.zabbix.com/download
Используем: Zabbix 6.0; Ubuntu 22.04; Component Server; PostgreSQL; Nginx

wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4+ubuntu22.04_all.deb	# запись файла в директорию /etc/apt/sources.list.d/
dpkg -i zabbix-release_6.0-4+ubuntu22.04_all.deb									#
ls /etc/apt/sources.list.d/												# проверка добавленных репозиториев
apt update													            	# перерега кэша
apt search zabbix												        	# проверка установки
apt install zabbix-server-pgsql zabbix-frontend-php php8.1-pgsql zabbix-nginx-conf zabbix-sql-scripts zabbix-agent	# установка ПО и модулей
sudo -u postgres createuser --pwprompt zabbix										# создание пользователя в PostgreSQL (задается пароль, ошибку можно игнорировать, это из-за входа через рута)
cat /etc/passwd													                      	# проверка пользователя
sudo -u postgres createdb -O zabbix zabbix									  	# создание группы в PostgreSQL (ошибку можно игнорировать, это из-за входа через рута)
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix			      	#
Открыть vim /etc/zabbix/zabbix_server.conf расскоментировать параметр DBPassword= и зададать пароль
Открыть vim /etc/zabbix/nginx.conf расскоментировать listen 8080 и прописать порт 80 расскоментировать server_name и прописать имя 192.168.163.149
sudo nginx -t
systemctl restart nginx	zabbix-server zabbix-agent nginx php8.1-fpm							# перезапуск веб-сервера и служб
systemctl enable nginx zabbix-server zabbix-agent nginx php8.1-fpm							# добавление в автозагрузку веб-сервера и служб
Перейти в браузере по ссылке 192.168.163.149
Файл конфигурации "conf/zabbix.conf.php" создастся после первоначальной настройки.

------------------------------------------------------------------------------------------ШАГ 4------------------------------------------------------------------------------------------

установка агента
https://www.zabbix.com/download_agents
