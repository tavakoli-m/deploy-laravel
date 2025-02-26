# نحوه استقرار (Deploy) پروژه لاراولی روی سرور مجازی (VPS) Ubuntu 24

### ابتدا به از طریق SSH به سرور خودتون متصل بشید
```sh
Syntax:- ssh -p PORT USERNAME@IP

Example:- ssh -p 22 root@192.168.1.1
```
**نکته** پورت پیش فرض 22 است اگر پورت را دستی عوضی کردید باید پورتی که ست کردید رو بجای 22 قرار بدید

### سرور رو اپدیت کنید
```sh
apt update && apt upgrade -y
```

### کاربر جدید بسازید

```sh
Syntax:- adduser $USERNAME

Example:- adduser admin
```
**نکته** بعد از اینکه این دستور رو وارد کنید ازتون رمز عبور این کاربر رو میخاد رمز عبور قوی رو بهش بدید بعد از اینکه رمز عبور رو بهش دادید اگر اطلاعاتی مثل اسم و شماره تلفن اینجور چیزا خواست با زدن دکمه اینتر میتونید رد بشید ازش و اگر هم دوست داشتید میتونید اون اطلاعات رو بدید بهش

### اعطا دسترسی ادمین به کاربر ساخته شده

```sh
Syntax:- usermod -aG sudo $USERNAME

Example:- usermod -aG sudo admin
```

### عوض کردن پورت و غیر فعال کردن ورود با کاربر root

دستور زیر رو اجرا کنید

```sh
nano /etc/ssh/sshd_config
```

سپس دنبال کلیدی با عنوان PORT بگردید اگر اولش # بود به این معنی هست که کامنت هستش اول از اون # حذف کنید بعدش جلوش یه عدد 4 یا 5 رقمی قرار بدید البته اون عدد که میشه همون پورت رو باید زخیره کنید یجا چون بعدا برای ورود به سرور بهش نیاز دارید

بعدش که پورت رو عوض کردید الان دنبال کلیدی با عنوان PermitRootLogin بگردید جلوش عبارت yes نوشته شده اون رو به no تغیر بدید بعدش با زدن دکمه های کنترل و x و سپس تایید تغیرات و سیو اون ها فایل براتون بسته میشه


### ری استارت کردن سرور برای اعمال همه تغیرات
خب الان که کانفیگ اولیه رو انجام دادید بهتره که سرور رو یبار ری استارت کنیم تا همه تغیرات اعمال بشه با دستور زیر میتونید سرور رو ری استارت کنید

```sh
reboot
```

**نکته**
حالا که ورود با کاربر روت رو جلوش رو گرفتیم و پورت رو عوض کردیم موقع SSH زدن به سرور باید با کاربری که خودمون ساختیم وارد بشیم و پورتی که خودمون ست کردیم رو هم توی SSH بزنیم برای مثال

```sh
Syntax:- ssh -p PORT USERNAME@IP

Example:- ssh -p 1413 admin@192.168.1.1
```

### نصب دیواره اتش (Firewall) و کانفیگ ان
داخل سرور ها برای اینکه امنیت سرور بالا بهبود پیدا کنه یه نرم افزاری رو نصب میکنن به اسم دیواره اتش که میاد برای ما ترافیک ورودی و ترافیک خروجی رو نحوه رفت امدشون رو مدیریت میکنه

```sh
sudo apt install ufw

sudo ufw enable

sudo ufw default deny incoming

sudo ufw default allow outgoing
```

**نکته**
نصب دیواره اتش باعث میشه که همه پورت های غیر اون های که ما واسش مشخص کردیم بسته بشه که خب این موضوع باعث میشه که ما نتونیم دیگ به سرور درخواست SSH بزنیم برای ورود پس باید پورت SSH رو باز کنیم با دستور زیر میتونید یه پورت مشخصی رو برای سرور باز کنید

```sh
Syntax:- sudo ufw allow PORT

Example:- sudo ufw allow 1413
```
**نکته**
شما باید پورتی که برای خودتون ست کردید رو باز کنید نه بقیه پورت هارو


### باز کردن پورت درخواست های HTTP و HTTPS

با زدن دستور های زیر کاربر ها میتونن به سرور شما درخواست بدن برای دیدن سایت پس حتما باید این دوتا پورت رو باز کنید

```sh
sudo ufw allow http

sudo ufw allow https
```

### نصب محافظ SSH
برای محافظت بیشتر از SSH و پورتی که برای اون باز هست و جلوگیری از یه سری حمله ها بهتره که SSH GUARD رو نصب کنیم

```sh
sudo apt install sshguard
```

### نصب وب سرور NGINX

```sh
sudo apt install nginx -y

sudo systemctl enable nginx
```

### نصب ورژن کنترلر GIT

```sh
sudo apt install  git -y
```

### نصب PHP 8.4 روی سرور

```sh
sudo apt install software-properties-common gnupg2 apt-transport-https lsb-release ca-certificates -y

sudo add-apt-repository ppa:ondrej/php

sudo apt install php8.4 php8.4-cli -y
```
برای برسی نصب درست php دستور زیر رو وارد کنید تا چک کنید ورژن درست نصب شده یا خیر

```sh
php --version
```

برای نصب ماژول های پرکاربر php که معمولا روی سرور ها نصب میشن از دستور زیر استفاده کنید

```sh
sudo apt install php8.4-common php8.4-fpm php8.4-mysql php8.4-xml \
php8.4-gd php8.4-mbstring php8.4-zip php8.4-bcmath php8.4-curl php8.4-redis -y
```
برای راه  اندازی خودکار PHP FPM موقع بوت سرور دستور زیر رو اجرا کنید

```sh
sudo systemctl enable php8.4-fpm
```

### نصب Composer 

```sh
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"

php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'.PHP_EOL; } else { echo 'Installer corrupt'.PHP_EOL; unlink('composer-setup.php'); exit(1); }"

php composer-setup.php

php -r "unlink('composer-setup.php');"
sudo mv composer.phar /usr/local/bin/composer
```

برای برسی نصب درست composer با دستور زیر ورژن اون رو چک کنید

```sh
composer -V
```

### نصب NodeJS
برای نصب NodeJs دستور های زیر رو به ترتیب اجرا کنید
```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

\. "$HOME/.nvm/nvm.sh"

nvm install 22

node -v


nvm current

npm -v
```

بعد از اینکه NodeJs با موفقیت نصب شد دستور های زیر رو بزنید تا از نصب موفق اطمینان پیدا کنید

```sh
node -v #Should print "v22.14.0"
npm -v #Should print "10.9.2".
```


### نصب MySql 

```sh
sudo apt install mysql-server -y

sudo systemctl enable mysql
```

برای افزایش امنیت دیتابیس MySql پکیج زیر رو نصب کنید به ترتیب گزینه هاش رو مثل شکل زیر بزنید

```sh
sudo mysql_secure_installation
```

```sh
1 => y

2 => 2

3 => y

4 => y

5 => y

6 => y
```

بعدش برای وصل شدن به دیتابیس دستور زیر رو بزنید

```sh
sudo mysql
```

بعدش رمز کاربری کاربر root دیتابیس رو عوض کنید, بجای $PASSWORD رمز عبور خودتون رو بزارید

```sh
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '$PASSWORD';
```

بعد یه دیتابیس جدید بسازید, دقت کنید بجای $DBNAME باید اسم دیتابیس رو بزارید

```sh
CREATE DATABASE $DBNAME;
```

بعدش یه کاربر جدید بسازید, دقت کنید بجای $USERNAME باید نام کاربر رو بزارید و بجای $PASSWORD باید رمز عبور کاربر رو بزارید

```sh
CREATE USER '$USERNAME'@'localhost' IDENTIFIED BY '$PASSWORD';
```

بعدش تمام دسترسی های دیتابیس رو به اون کاربر بدید

```sh
GRANT ALL PRIVILEGES ON $DBNAME.* TO '$USERNAME'@'localhost';
```

و بعدش دستور زیر رو وارد کنید تا همه تغیرات اعمال بشن

```sh
FLUSH PRIVILEGES;
```

بعد از انجام دادن کار و خروج از محیط دیتابیس از  دستور زیر استفاده کنید

```sh
exit;
```

### انتقال پروژه به سرور

اول پروژه خوتون رو داخل یه ریپو خصوصی پوش کنید

بعدش دستور زیر رو وارد کنید تا یک کلید ssh براتون ساخته بشه دقت کنید بجای $EMAIL باید ایمیل خودتون رو قرار بدید


```sh
Syntax:- ssh-keygen -t ed25519 -C "@EMAIL"
Example:- ssh-keygen -t ed25519 -C "mail@gmail.com"
```

بعدش دستور زیر رو وارد کنید

```sh
cat ~/.ssh/id_ed25519.pub
```

دستور بالا بهتون یه خروجی میده کل اون خروجی رو کپی کنید و برید داخل ریپو گیت هاب بعدش داخل تنظیمات بعدش از منو سمت چپ گزینه ***Deploy keys*** رو انتخاب کنید روی گزینه Add کلیک کنید و یه عنوان براش انتخاب کنید مهم نیست چی باشه و به عنوان کلیدش اون مقداری که از سرور کپی کردید رو قرار بدید و کلید رو اضافه کنید

وارد مسیر ***/var/www*** داخل سرورتون بشید و مالکیت اون رو عوض کنید با دستور زیر

```sh
cd /var/www

sudo chown $USER:$USER .
```

الان با استفاده از ***SSH*** پروژه رو کلون کنید دقت کنید بجای $URL باید ادرس ریپو خصوصی گیت هاب رو قرار بدید


```sh
Syntax:- git clone $URL

Example:- git clone git@github.com:tavakoli-m/deploy-laravel.git
```

الان وارد پروژه تون بشید و پکیج های برنامه رو نصب کنید

```sh
cd deploy-laravel

composer install --no-dev
```

الان فایل کافینگ پروژه رو کپی کنید 

```sh
cp .env.example .env
```

کلید برنامه رو ست کنید

```sh
php artisan key:generate
```

برای مجوز های پروژه دستور های زیر رو به ترتیب ران کنید

```sh
sudo chown -R www-data:www-data storage

sudo chown -R www-data:www-data bootstrap/cache

sudo usermod -a -G www-data $USER

sudo find storage -type f -exec chmod 644 {} \;

sudo find storage -type d -exec chmod 775 {} \;
```

الان وارد ***.env*** بشید و کافیگ های خودتون مثل دیتابیس و ... رو ست کنید یه نمونه کافینگ ابتدایی روز این زیر براتون میزارم

```sh
APP_NAME=Laravel
APP_ENV=production
APP_KEY=run php_artisan_key:generate
APP_DEBUG=false
APP_URL=http://your-domain.com/

LOG_CHANNEL=stack
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=$DBNAME
DB_USERNAME=$USERNAME
DB_PASSWORD=$PASSWORD
```

با دستور زیر جداول رو بسازید

```sh
php artisan migrate
```

### نصب وابستگی های فرانت
برای نصب وابستگی های فرانت پروژه با استفاده از NodeJs دستور های زیر رو به ترتیب اجرا کنید

```sh
npm install --omit=dev

npm run build
```

لینک کنید storage رو public 

```sh
php artisan storage:link
```


### تنظیمات NGINX برای Laravel

برای اجرای برنامه باید NGINX رو کافیگ کنید تا پروژه اماده استفاده بشه

یه فایل داخل پوشه /etc/nginx/sites-available بسازید و فایل های کانفیگ زیر رو داخلش قرار بدید البته بجای $YOUR_DOMIAN باید اسم دامین خودتون رو بزارید

```sh
Syntax:- sudo nano /etc/nginx/sites-available/$YOUR_DOMIAN$
Example:- sudo nano /etc/nginx/sites-available/mohammadrezatavakoli.ir
```

بعدش کافیگ زیر رو وارد کنید

البته بجای $PROJECT_FOLDER نام فولدر پروژه خودتون و قرار بدید و بجای $YOUR_DOMIAN نام دامنه خودتون رو


```sh
server{
    listen 80;
    listen [::]:80;
    server_name $YOUR_DOMIAN;
    root /var/www/$PROJECT_FOLDER/public;
    index index.php index.html;
    location / {
         try_files $uri $uri/ /index.php$is_args$args;
    }
    location ~ \.php$ {
        fastcgi_pass unix:/run/php/php8.4-fpm.sock;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
    }
}
```

بعد از اینکه این تنظیمات رو قرار داد با استفاده از دستور زیر فایل رو لینک کنید تا فعال بشه سایت براتون

```sh
sudo ln -s /etc/nginx/sites-available/$YOUR_DOMIAN /etc/nginx/sites-enabled/
```

### ست کردن DNS برای دامنه

وارد DNS Provider خودتون بشید و رکورد های به شکل زیر بسازید

| Type | Host/Name | Value |
| :---: | :---: | :--- |
| A     | @     | Your Remote Server IP |


### اپدیت دستی پروژه

برای اپدیت  پروژه و دریافت کد های جدید پروژه از مخزن گیت هاب کافی فقط دستور زیر رو اجرا کنید

```sh
git pull
```
کد بالا میاد تمام تغیرات رو میگیره و داخل سورس پروژه قرار میده ولی شما خودتون باید بیاید دستور های مثل اجرا مایگرشن ها یا نصب وابستگی فرانت و بکند رو اجرا کنید که کار خیلی حوصله سر بر و حساسی هست چون ممکنه مشکلاتی رو برای پروژه به وجود بیاره اگر بخاید دستی اجراش کنید در ادامه نحوه راه اندازی استقرار خودکار رو بهتون میگم

### اجرای سیستم صف (Queue) لاراول

برای راه اندازی سیستم صف لاراول نیاز به یک ابزاری داریم به اسم supervisor تا صف های ما درحالت اجرا بمونن برای نصب این ابزار ابتدا دستور زیر رو وارد کنید

```sh
sudo apt install supervisor
```

بعدش یه فایل داخل فولدر ***conf.d*** در مسیر ***/etc/supervisor/conf.d*** دارید تا تون رو پیکربندی کنید

```sh
sudo nano /etc/supervisor/conf.d/laravel-worker.conf
```

و داخلش این کانفیگ رو قرار بدید

```sh
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /var/www/$PROJECT_FOLDERt/artisan queue:work --sleep=3 --tries=3 --max-time=3600
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true
user=www-data
numprocs=8
redirect_stderr=true
stdout_logfile=/var/www/$PROJECT_FOLDER/storage/logs/larave-queue-worker.log
stopwaitsecs=3600
```

بعدش برای راه اندازی سیستم صف دستور های زیر رو به ترتیب اجرا کنید

```sh
sudo supervisorctl reread
 
sudo supervisorctl update
 
sudo supervisorctl start "laravel-worker:*"
```

### اجرای سیستم زمان بندی (Schedule) لاراول

برای راه اندازی سیستم زمان بندی لاراول فقط کافی که این دستور رو اجرا کنید

```sh
crontab -e
```

اگر ازتون خواتس که انتخاب کنید با کدوم ادیتور این رو باز کنم فقط گافی گزینه اول یا همون nano رو انتخاب کنید

بعد از این که ادیتور کرون جاب واستون باز شد فقط کافی که خط زیر رو به اخرش اضافه کنید و بجای $PROJECT_FOLDER اسم فولدر پروژه خودتون رو بزارید

دقت کنید کافیگ زیر هر دقیقه دستور رو اجرا میکنه اگر میخاید عوضش کنید باید بجای اون 5 تا * اولش زمان بندی خودتون رو ست کنید

```sh
* * * * * cd /var/www/$PROJECT_FOLDER && php artisan schedule:run >> /var/www/$PROJECT_FOLDER/storage/logs/schedule.log 2>&1
```

### راه اندازی استقرار خودکار

برای راه اندازی استقرار خودکار چند تا کار ساده باید انجام بدید 

اولیش اینه که داخل روت اصلی پروژه تون یه فولدر بسازید به اسم ***.scripts*** بعدش داخلش یه فایل درست کنید به اسم ***deploy.sh*** و دستورات زیر رو داخلش قرار بدید
دقت کنید به جای ***$USERNAME*** باید اسم کاربری که خودتون برای سیستم ساختید رو بدید بهش (توی تول مستندات اسمش رو admin گزاشتیم برای مثال)

```sh
#!/bin/bash
set -e

echo "Deployment Started ..."

# Turn ON Maintenance Mode or return true
# if already is in maintenance mode
(php artisan down) || true

# Pull the latest version of the app
git pull origin main

# Install composer dependencies
composer install --no-dev

export PATH=$PATH:/usr/local/bin:/home/$USERNAME/.nvm/versions/node/v22.14.0/bin

# Install npm dependencies
npm install --omit=dev

npm run build

# Clearing Cache
php artisan cache:clear
php artisan config:clear

# Recreate cache
php artisan optimize

# Run database migrations
php artisan migrate --force

# Restart Queue Workers

php artisan queue:restart

# Turn OFF Maintenance mode
php artisan up

echo "Deployment Finished!"
```

بعدش  داخل ترمینال داخل سیستم لوکال خودتون دستور های زیر رو به ترتیب اجرا کنید 

```sh
cd ./.scripts 

git update-index --add --chmod=+x deploy.sh
```

قدم بعدی باید داخل روت پروژه یه فولدر بسازید به اسم ***.github*** و بعدش داخل اون هم یه فولدر بسازید به اسم ***workflows*** و بعدش هم داخل اون یه فایل درست کنید به اسم ***deploy.yml*** و دستورات زیر رو داخلش قرار بدید
دقت کنید بجای $PROJECT_FOLDER باید نام فولدر پروژه تون داخل سرور قرار بگیره

```yml
name: Deploy

# Trigger the workflow on push and
# pull request events on the master branch
on:
  push:
    branches: ["main"]
  pull_request:
    branches: ["main"]

# Authenticate to the the server via ssh
# and run our deployment script
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Deploy to Server
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.HOST }}
          username: ${{ secrets.USERNAME }}
          port: ${{ secrets.PORT }}
          key: ${{ secrets.SSHKEY }}
          script: "cd /var/www/$PROJECT_FOLDER && ./.scripts/deploy.sh"
```

بعدش برید داخل ریپو پروژه و وارد بخش تنظیمات بشید از منو سمت چپ گزینه ***Secrets and Variables*** رو انتخاب کنید و بعدش وارد ***Actions*** بشید داخل بخش های ***Secrets*** چند کلید ست کنید به عناوین زیر

```sh
Name: HOST
Secret: Your_Server_IP
```

دقت کنید ***$PORT*** همون پورتی باشه که برای SSH توی اول اموزش ست کردید
```sh
Name: PORT
Secret: $PORT
```


دقت کنید ***$USERNAME*** باید نام کاربری همون یوزری باشه که ساختید

```sh
Name: USERNAME
Secret: $USERNAME
```


الان باید کلید خصوصی SSH رو توی این ست کنید برای دریافت کلید خصوصی که ساختیم باید دستور زیر رو اجرا کنید داخل سرورتون و بعدش خروجی رو به طور کامل پی و داخل این ست کنید

```sh
cat ~/.ssh/id_ed25519
```

دستور بالا بتون یه کلید میده کل اون رو کپی کنید به به کلیدی به شکل زیر ست کنید

```sh
Name: SSHKEY
Secret: your_ssh_key
```

الان فایل کلید عمومی رو باز کنید و کلید رو کپی کنید  داخل کلید های اجازه داده شده اضافه کنید

```sh
cat ~/.ssh/id_ed25519.pub

sudo nano  ~/.ssh/authorized_keys
```


تغیرات رو از داخل لوکال به داخل ریپو گیت هاب پوش کنید

و بعدش برای اخرین بار وارد سرورتون بشید و تغیرات رو به دستور ***git pull*** از ریپو دریافت کنید و دیگ کار تموم الان با پوش کردن هر تغیر داخل ریپو خوکار کد سرور شما هم اپدیت میشه و در اختیاز کاربرها قرار میگیره  دقت کنید اگر که pull request داخل گیت هاب ساخته بشه و اون مرج بشه با برنچ اصلی بازم کد های شما به صورت خودکار دیپلوی میشن 
شما نتیجه این استقرار هارو میتونید داخل ریپو پروژه داخل صفحه ***Actions*** ببینید

خب دیگ تموم شد الان وارد دامنه خوتون بشید و سایت رو چک کنید