Todos os arquivos base utilizados neste projeto são de código aberto e não foram desenvolvidos por mim. Os templates, por sua vez, foram personalizados para atender às especificações do trabalho, facilitando a análise de dados e a interação com o dispositivo. 
A seguir, apresentamos um guia passo a passo para a instalação do servidor.

Esse manual contempla a instalação do Zabbix Server versão 6.0 no Ubuntu desktop versão 22.04 LTS.
Procedimento testado, alguns repositórios podem ser alterados por repositórios atualizados do site da Zabbix.

Todos as linhas comentadas “#” devem ser inseridas no terminal Linux em modo “ROOT”.

Entrar em modo root no Ubuntu:
# sudo -i

Link do repositório:
https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/

Instalação:
Temos que instalar o MariaDB a parte, pois há um bug nos repositórios do Zabbix que não permite a instalação.
sudo apt update
sudo apt install mariadb-server mariadb-client mariadb-backup
systemctl enable --now mariadb
sudo mysql_secure_installation

- Sua senha de acesso ao BD será de seu usuário root, pois nessa versão eles usam o MariaDB no lugar do MySQL.

Instalação do MariaDB:
# mysql -uroot -p
# mysql> create database zabbix character set utf8 collate utf8_bin;
# mysql> grant all privileges on zabbix.* to zabbix@localhost identified by 'zabbix';
# mysql> quit;

Agora, vamos instalar o Zabbix
# cd /usr/src
# wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4%2Bubuntu22.04_all.deb
# dpkg -i zabbix-release_6.0-4+ubuntu22.04_all.deb
# apt update 

Instalação:
# apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent

Agora, vamos carregar as informações para dentro do nosso Banco de Dados!
Caso precise, localize o arquivo com esses comandos:
# apt install locate
# updatedb
# locate server.sql.gz

Ao encontrar, vamos continuar!
# zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql -uzabbix -p zabbix

A senha é: zabbix

# apt install vim
# vim /etc/zabbix/zabbix_server.conf

DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=zabbix

# vim /etc/apache2/conf-available/zabbix.conf

- Descomentar a seguinte linha:
- php_value date.timezone Europe/Riga

- Alterar para:
- php_value date.timezone America/Sao_Paulo

E vamos instalar o PT-BR:
# locale -a
# locale-gen pt_BR.UTF-8

Agora, vamos dar um restart em todos os serviços:
# systemctl restart zabbix-server zabbix-agent apache2
# systemctl enable zabbix-server zabbix-agent apache2
# systemctl status zabbix-server zabbix-agent apache2

Ache o seu IP:
# hostname -I

Acessar interface Web:
http://<IP_do_host>/zabbix

Login: Admin (com A maiúsculo)
Senha: zabbix


