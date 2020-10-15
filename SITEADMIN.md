# Site Admin 설정

기본사항
~~~
docker, docker compose 실행환경
apache(php)는 utf8로 설정되고, timezone은 Asia/Seoul로 설정되어야 함.
mysql의 character set은 utf8로 설정되고, collation은 utf8_general_ci로 설정되어야 함.
~~~

### Docker composer 실행

~~~
docker-compose.yml 파일의 vhosts의 DOCUMENT_VHOSTS의 ROOT 폴더 설정(개인설정에 따름)

services:
  ...
  webserver:
  ...
    volumes: 
    ...
      - ${DOCUMENT_VHOSTS-../../php-workspace}:/var/www/vhosts
      
$ docker-compose up -d
~~~

### 데이터베이스 Import

~~~
Scheman 생성 : gifttopping
User 생성 : giftuser / ********

Import Data : dump 파일 이용

$ create database gifttopping;
$ mysql -u giftuser -p gifttopping < backup.dmp

MySQL 상세설명은 생략(Workbench를 이용하면 편리함)

mysqld.cnf에 override된 설정 적용
# character set
skip-character-set-client-handshake
character-set-server = utf8
collation-server = utf8_general_ci
# tiome zone
default-time-zone='Asia/Seoul'
#  sql mode
sql_mode="NO_ENGINE_SUBSTITUTION" -- 기본값 : ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
~~~

### Virtual Host 추가

~~~
가상 도메인 추가
예 : tworking.gifttopping.local(/ets/hosts 파일에 추가)

config/vhosts/siteadmin.conf 파일 추가
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot "/var/www/vhosts/siteadmin"
    ServerName tworking.gifttopping.local
	<Directory "/var/www/vhosts/siteadmin/">
		AllowOverride all
	</Directory>
</VirtualHost>

Apache 재실행
$ docker exec -t 5.6.x-webserver service apache2 restart
~~~

http://tworking.gifttopping.local/
