# Redmine

---

![Untitled](https://user-images.githubusercontent.com/84123877/209275345-61ba288c-10ad-4934-85af-126e88079ef1.png)

레드마인(Redamine)은 오픈소스 프로그램으로 웹 기반의 프로젝트 관리와 버그 추적 기능을 제공하는 도구이다. 주요 기능으로는,

- 달력과 칸트 차트
- 일정관리 기능

화면기반의 기능을 제공하여 프로젝트 관리에 도움을 줄 수 있다.

redmine의 디자인은 Trac에 영향을 많이 받았으며 `ruby`에 기반하여 작성되었고 멀티 플랫폼을 지원하며 여러가지 종류의 데이터베이스 및 34개의 언어를 지원한다.

---

## Ubuntu 20.04 LTS , install

### database(mariaDB) 설치

```bash
# database를 설치한다.
apt-get update
apt-get install mariadb-server -y
mysql -u root -p

# db 버전에 따라서 utf8로 진행하여도 된다.
MariaDB > create database redmine character set utf8m64;
MariaDB > GRANT ALL PRIVILEGES ON redmine.* TO 'redmine'@'localhost' IDENTIFIED BY 'passwd';
exit
```

### apache 설치

```bash
# apache 설치 및 버전 확인
apt-get install -y apache2

apache2 -v
Server version: Apache/2.4.41 (Ubuntu)
Server built:   2022-06-14T13:30:55

apachectl -M | grep passenger
AH00558: apache2: Could not reliably determine the server's 
fully qualified domain name, using 127.0.1.1. Set the 'ServerName' 
directive globally to suppress this message
```

### Redmine 설치

```bash
# Redmine 설치
apt-get install redmine redmine-mysql -y
```

![Untitled 1](https://user-images.githubusercontent.com/84123877/209275323-24092ba3-974c-471c-a866-5d23ecd80374.png)

![Untitled 2](https://user-images.githubusercontent.com/84123877/209275330-f12fe4cb-b662-46ef-8f75-81a6e095e31a.png)

![Untitled 3](https://user-images.githubusercontent.com/84123877/209275331-20e2485d-c7ce-4834-a3a3-f3e7b46f376b.png)

### gem 업데이트, bundler 설치

```bash
# gem 업데이트, bundler 설치
gem -v
gem update
```

![Untitled 4](https://user-images.githubusercontent.com/84123877/209275332-0cdda92d-4b9b-45fe-b199-ffd58ca1e167.png)

/var/lib/gems 하위 디렉터리에 접근하기 위해서는  

파일 permission을 chmod로 수정하거나, sudo 로 명령어를 지시하는 방법이 있다.

```bash
sudo gem install bundler
```

### apache 설정

redmine web interface를 apache에 연결한다.

```bash
sudo vi /etc/apache2/mods-available/passenger.conf

<IfModule mod_passenger.c>
  PassengerRoot /usr/lib/ruby/vendor_ruby/phusion_passenger/locations.ini
  PassengerDefaultRuby /usr/bin/ruby
  PassengerDefaultUser www-data
  PassengerRuby /usr/bin/ruby
</IfModule>
```

apache의 000-default.conf에 내용을 추가한다.

```bash
sudo ln -s /usr/share/redmine/public /var/www/html/redmine
sudo ll /var/www/html/redmine

sudo vi /etc/apache2/sites-available/000-default.conf

<Directory /var/www/html/redmine>
    RailsBaseURI /redmine
    PassengerResolveSymlinksInDocumentRoot on
</Directory>
```

apache의 www-data 사용자가 엑세스 할 수 있도록 Gemfile.lock을 생성하고 권한을 부여한다.

```bash
touch /usr/share/redmine/Gemfile.lock

sudo chown -R www-data:www-data /usr/share/redmine/
sudo chmod -R 755 /usr/share/redmine/
```

구성 설정 적용을 위해 apache를 재시작한다.

```bash
sudo service apache2 restart
&
systemctl restart apache2

재시작 과정에서 error가 발생시, 로그 확인
systemctl status apache2.service
sudo journalctl -xe
&
cd /etc/apache2/
apache2ctl configtest
```

### Port 변경

```bash
sudo vi /etc/apache2/ports.conf

Listen xxxx
```

---

### Redmine 접속

![Untitled 5](https://user-images.githubusercontent.com/84123877/209275333-ba34ef45-44a9-4451-82e4-20cf57054c40.png)

`http://localhost/redmine` 접속

![Untitled 6](https://user-images.githubusercontent.com/84123877/209275336-08e17818-5595-4bd6-bfce-723864791584.png)

![Untitled 7](https://user-images.githubusercontent.com/84123877/209275339-d82b8931-5727-4290-80d7-51c6a627cf7f.png)

![Untitled 8](https://user-images.githubusercontent.com/84123877/209275341-999eb95a-9098-4989-8b75-7a167c55eec9.png)

![Untitled 9](https://user-images.githubusercontent.com/84123877/209275344-935df1ac-8656-4d4a-8daf-585d8b6a004e.png)

테스트로 프로젝트와 일감을 추가한 모습이다. 이렇게 redmine 구축 및 테스트를 진행을 완료하였다.  

redmine은 오픈소스 프로그램인 만큼 자유도는 높지만 기능 구현에는 자세한 학습이 필요해 보인다.

---
