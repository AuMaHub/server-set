
> 사양
> GCP 210$/M
> - cpu : 8코어
> - ram : 16GB
> - disk : 40GB

```
ssh -i ~/.ssh/FrwksAuma 34.64.000.000

sudo apt update -y
sudo apt upgrade -y
sudo apt install nodejs -y
sudo apt install mysql-server -y
sudo apt install nginx -y
sudo apt install npm -y
sudo apt remove cmdtest
sudo apt remove yarn
curl -sS <https://dl.yarnpkg.com/debian/pubkey.gpg> | sudo apt-key add -
echo "deb <https://dl.yarnpkg.com/debian/> stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt-get update
sudo apt-get install yarn -y

sudo npm install -g yarn
sudo npm install -g pm2
pm2 install pm2-logrotate

sudo ufw allow mysql

sudo systemctl start mysql

sudo systemctl enable mysql

sudo mysql -uroot -p

## mysql command start

ALTER user 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '1234';
FLUSH PRIVILEGES;

CREATE DATABASE fairy;
CREATE USER 'student'@'%' IDENTIFIED BY '1234';
FLUSH PRIVILEGES;
GRANT ALL PRIVILEGES ON fairy.* TO 'student'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;

exit

## mysql command end

echo "sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION" | cat /etc/mysql/mysql.conf.d/mysqld.cnf -
sudo /etc/init.d/mysql restart

mkdir ~/project/
mkdir ~/project/front
mkdir ~/project/back

sudo git clone https://{token}@github.com/AuMaHub/fw-fairy-fe.git ~/project/front
sudo git clone https://{token}@github.com/AuMaHub/fw-fairy-be.git ~/project/back

sudo vi /etc/nginx/sites-available/default

```
    location / {
            root /home/auma/project/front;
            proxy_pass http://localhost:3000/;
    }

    location /api/ {
            proxy_pass http://localhost:8000/;

    }
```

sudo touch ~/project/front/.env
sudo echo "NEXT_PUBLIC_BASE_URL=<http://34.64.000.000:3000>
NEXT_PUBLIC_API_URL=<http://34.64.000.000/api>" >> ~/project/front/.env -

sudo touch ~/project/back/.env
sudo echo "NODE_ENV=local
PORT=8000
API_URL=<http://34.64.000.000/api/>
CRYPTO_SECRET_KEY=fairy
JWT_SECRET=fairy
JWT_ISSUER=fairy
DB_HOST=localhost
DB_USERNAME=student
DB_PASSWORD=1234
DB_DATABASE=fairy
DB_ENC_SECRET_KEY=fairy" >> ~/project/back/.env

sudo npm cache clean -f
sudo npm install -g n
sudo n stable

cd ~/project/front/
sudo yarn install
sudo yarn dev

# ctrl + c로 탈출

cd ~

cd ~/project/back/
sudo yarn install
sudo node app.js

# ctrl + c로 탈출

cd ~

sudo pm2 start "sudo yarn --cwd /home/auma/project/front/ dev" --name 'front' -p 3000
sudo pm2 start "sudo node /home/auma/project/back/app.js" --name 'api' -p 8000

sudo pm2 list
```