# Guide

Here is your **full, production-ready, copy-paste GitHub `README.md`** for Linux project migration **without Docker or Git**, covering Python, Java, PHP, Ruby, Maven, “Sintara”, project transfer, configuration, setup, verification, and extra admin tips.

---
Here you go — a **Windows-only version** of the scripts.
No Linux commands.
You can run these in **PowerShell** or **Command Prompt**.

---

# ✅ **1. Prerequisites (Windows)**

### **Install AWS CLI**

Download & install:
(Windows installer → AWS CLI v2)

### **Configure AWS CLI**

```powershell
aws configure
```

Enter:

* AWS Access Key
* AWS Secret Key
* Region: **us-east-1**
* Output: json

### **Install MySQL Client (Windows)**

Install from MySQL Installer:
Select → **MySQL Shell / MySQL Client**.

---

# 🚀 **2. PowerShell Script: Create RDS MySQL Instance in AWS**

Save as: **create-rds.ps1**

```powershell
# Variables
$DBInstance = "mysql-lab-01"
$DBName = "classicmodels"
$User = "admin"
$Pass = "YourStrongPassword123!"
$Region = "us-east-1"

Write-Host "Creating RDS MySQL Instance..."

aws rds create-db-instance `
    --db-instance-identifier $DBInstance `
    --allocated-storage 20 `
    --db-instance-class db.t3.micro `
    --engine mysql `
    --engine-version "8.0.35" `
    --master-username $User `
    --master-user-password $Pass `
    --backup-retention-period 0 `
    --no-multi-az `
    --publicly-accessible `
    --storage-type gp2 `
    --region $Region `
    --tags Key=name,Value=mysql-lab-01 Key=project,Value=mysql-lab Key=roll_no,Value=YOURROLL Key=date,Value=TODAYS_DATE
```

Run it:

```powershell
powershell -File create-rds.ps1
```

---

# 📡 **3. Script to Get RDS Endpoint (Windows)**

```powershell
aws rds describe-db-instances --db-instance-identifier mysql-lab-01 --query "DBInstances[0].Endpoint.Address" --output text
```

Example output:

```
mysql-lab-01.abcxyz12345.us-east-1.rds.amazonaws.com
```

---

# 🔗 **4. Connect to RDS MySQL (Windows Terminal)**

Replace `<endpoint>`:

```cmd
mysql -h <endpoint> -u admin -p
```

Enter your password.

---

# 📥 **5. Import SQL File to RDS (Windows)**

Place **classicmodels.sql** in the same folder.

```cmd
mysql -h <endpoint> -u admin -p classicmodels < classicmodels.sql
```

If DB does not exist, create it:

```cmd
mysql -h <endpoint> -u admin -p -e "CREATE DATABASE classicmodels;"
```

---

# 📤 **6. Export Database from RDS (Windows)**

Export to a file:

```cmd
mysqldump -h <endpoint> -u admin -p classicmodels > classicmodels_export_01.sql
```

---

# ☁️ **7. Upload Export to S3 (Windows)**

First create the bucket:

```powershell
aws s3 mb s3://rollno-bucket-01 --region us-east-1
```

Upload the SQL file:

```powershell
aws s3 cp classicmodels_export_01.sql s3://rollno-bucket-01/
```

---

# 🎉 **DONE**



# **Project Migration Guide (No Docker / No Git)**

## **1. Introduction**

This guide provides a complete, step-by-step process for migrating any project to a fresh Linux server **without using Docker or Git**.
It includes instructions to install all common backend stacks (Python, Java, PHP, Ruby, Maven) and general project setup steps for administrators unfamiliar with the project.

---

## **2. Prerequisites**

Ensure you have the following prepared:

* SSH access to the Linux machine
* `sudo` privileges
* Project folder available locally (to transfer via SCP, rsync, FileZilla, SFTP, etc.)
* Knowledge of basic project requirements (e.g., Python, PHP, Java)

---

## **3. Installation Steps**

---

## **3.1 System Updates**

### **Debian / Ubuntu**

```bash
sudo apt update && sudo apt upgrade -y
```

### **Red Hat / CentOS / AlmaLinux / Rocky**

```bash
sudo yum update -y    # or
sudo dnf update -y
```

---

## **3.2 Core Dependencies**

---

### **Python & Pip**

#### **Install Python 3 + Pip + venv (Debian/Ubuntu)**

```bash
sudo apt install python3 python3-pip python3-venv -y
```

#### **Install Python 3 + Pip (RHEL/CentOS)**

```bash
sudo yum install python3 python3-pip -y
```

#### **Create and activate virtual environment**

```bash
python3 -m venv venv
source venv/bin/activate
```

#### **Install Python dependencies**

```bash
pip install -r requirements.txt
```

---

### **Java (OpenJDK 11 or 17)**

#### **Debian/Ubuntu**

```bash
sudo apt install openjdk-11-jdk -y
# or
sudo apt install openjdk-17-jdk -y
```

#### **Red Hat/CentOS**

```bash
sudo yum install java-11-openjdk-devel -y
# or
sudo yum install java-17-openjdk-devel -y
```

---

### **PHP**

#### **Debian/Ubuntu (Example: PHP 8.1)**

```bash
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
sudo apt install php8.1 php8.1-cli php8.1-common php8.1-mysql -y
```

#### **Red Hat/CentOS (using Remi)**

```bash
sudo yum install epel-release yum-utils -y
sudo yum install http://rpms.remirepo.net/enterprise/remi-release-8.rpm -y
sudo yum module enable php:remi-8.1 -y
sudo yum install php php-cli php-common php-mysqlnd -y
```

---

### **Ruby**

#### **Debian/Ubuntu**

```bash
sudo apt install ruby-full -y
```

#### **Red Hat/CentOS**

```bash
sudo yum install ruby ruby-devel -y
```

#### **Install Bundler**

```bash
gem install bundler
bundle install
```

---

### **Maven**

#### **Debian/Ubuntu**

```bash
sudo apt install maven -y
```

#### **Red Hat/CentOS**

```bash
sudo yum install maven -y
```

---

### **Sintara (Clarification Required)**

The tool *"Sintara"* does not exist in standard Linux repositories. It may be:

* A private tool
* A typo (perhaps Sinatra — Ruby framework?)
* A proprietary binary

#### **If it's Sinatra (Ruby web framework)**

```bash
gem install sinatra
```

#### **If it’s a custom tool:**

1. Ask the project owner for its binary or source
2. Download manually:

```bash
wget <download-link>
chmod +x sintara
sudo mv sintara /usr/local/bin/
```

> ⚠️ **Please specify the correct tool name if different.**

---

## **3.3 Project-Specific Dependencies**

### **Python**

```bash
pip install -r requirements.txt
```

### **PHP (Composer)**

```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php
sudo mv composer.phar /usr/local/bin/composer
composer install
```

### **Ruby**

```bash
bundle install
```

### **Java (Maven)**

```bash
mvn clean install
```

---

# **4. Project Migration Steps**

---

## **4.1 Transfer Project Files**

### **Option 1: Using rsync**

```bash
rsync -avz /path/to/local/project/ user@server_ip:/opt/project/
```

### **Option 2: Using SCP**

```bash
scp -r /path/to/project user@server_ip:/opt/project/
```

### **Option 3: Using FileZilla**

* Connect using SFTP
* Upload project → `/opt/project/`

---

## **4.2 Configure Project**

### **Set environment variables**

```bash
export APP_ENV=production
export DB_HOST=localhost
export DB_USER=root
export DB_PASS=yourpassword
```

To persist them:

```bash
echo "export DB_HOST=localhost" | sudo tee -a /etc/profile
```

---

### **Set file permissions**

```bash
sudo chown -R $USER:$USER /opt/project
sudo chmod -R 755 /opt/project
```

---

### **Setup virtual environment (Python projects)**

```bash
cd /opt/project
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

---

### **PHP Permissions (if using Apache/Nginx)**

```bash
sudo chown -R www-data:www-data /opt/project
sudo chmod -R 755 /opt/project
```

---

## **4.3 Build and Run the Project**

### **Python (Django/Flask)**

```bash
python manage.py migrate
python manage.py runserver 0.0.0.0:8000
```

### **Node.js (if project includes frontend)**

```bash
npm install
npm run build
```

### **PHP (Apache/Nginx)**

Place project in:

```
/var/www/html/
```

Then restart server:

```bash
sudo systemctl restart apache2    # Ubuntu
sudo systemctl restart httpd      # CentOS
```

### **Ruby (Sinatra/Rails)**

```bash
bundle install
ruby app.rb
```

### **Java (Spring Boot)**

```bash
mvn clean package
java -jar target/*.jar
```

---

# **5. Verification**

### **Check running services**

```bash
sudo systemctl status apache2
sudo systemctl status nginx
sudo systemctl status mysql
```

### **Check logs**

```bash
tail -f /var/log/syslog
tail -f /var/www/project/logs/app.log
```

### **Test application**

* Visit: `http://server-ip:8000` (Python)
* Visit: `http://server-ip` (PHP)
* Check API endpoints:

```bash
curl http://localhost:8000/health
```

---

# **6. Additional Admin Tips**

### ✔ Use `screen` or `tmux` to keep apps running

```bash
sudo apt install tmux
tmux new -s myapp
```

### ✔ Secure server

```bash
sudo ufw allow 22
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable
```

### ✔ Enable firewall on Red Hat

```bash
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --add-service=https --permanent
sudo firewall-cmd --reload
```

### ✔ Create a systemd service (for production apps)

```bash
sudo nano /etc/systemd/system/myapp.service
```

Example:

```
[Unit]
Description=My App
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/opt/project
ExecStart=/usr/bin/python3 /opt/project/app.py
Restart=always

[Install]
WantedBy=multi-user.target
```

Enable it:

```bash
sudo systemctl daemon-reload
sudo systemctl enable myapp
sudo systemctl start myapp
```

---

If you'd like, I can also **generate this README as a downloadable file** (`README.md`) — just say **“create file”**.
Below is an **updated section you can add to your README.md**.
It covers **how to run projects after migration** across all stacks: Python, PHP, Java, Ruby, Node.js, and multi-service deployments.

You can paste it directly under the “Verification” section or I can **regenerate the full README with this integrated** — just tell me.

---

# **6. How to Run Projects After Migration (All Stacks)**

Once your project has been successfully migrated to Linux, use the following commands based on the project type.
Each section includes **development mode**, **production mode**, and **running as a service**.

---

## **6.1 Python Projects (Django, Flask, FastAPI)**

### **Activate Virtual Environment**

```bash
cd /opt/project/
source venv/bin/activate
```

---

### **A) Django**

#### **Run Development Server**

```bash
python manage.py runserver 0.0.0.0:8000
```

#### **Apply Migrations**

```bash
python manage.py migrate
```

#### **Collect Static Files (Production)**

```bash
python manage.py collectstatic
```

#### **Run Production Server (Gunicorn)**

```bash
gunicorn projectname.wsgi:application --bind 0.0.0.0:8000
```

---

### **B) Flask**

#### **Run Development Server**

```bash
export FLASK_APP=app.py
flask run --host=0.0.0.0 --port=5000
```

#### **Production (Gunicorn)**

```bash
gunicorn "app:app" --bind 0.0.0.0:8000
```

---

### **C) FastAPI**

```bash
uvicorn main:app --host 0.0.0.0 --port 8000
```

---

---

## **6.2 PHP Projects (Apache / Nginx)**

### **Move Project to Web Root**

```bash
sudo cp -r /opt/project /var/www/project
sudo chown -R www-data:www-data /var/www/project
```

### **Apache - Restart and Run**

```bash
sudo systemctl restart apache2
```

Access in browser:

```
http://SERVER_IP/project/
```

### **Nginx - Restart**

```bash
sudo systemctl restart nginx
```

---

### **Run PHP Built-In Development Server**

```bash
php -S 0.0.0.0:8000 -t public
```

---

---

## **6.3 Java Projects (Spring Boot / Maven)**

### **Build Project**

```bash
mvn clean package
```

### **Run JAR**

```bash
java -jar target/*.jar
```

Application runs at:

```
http://SERVER_IP:8080
```

---

### **Run in Background**

```bash
nohup java -jar target/*.jar &
```

---

---

## **6.4 Ruby Projects (Sinatra, Rails)**

### **Sinatra**

```bash
ruby app.rb
```

### **Ruby on Rails**

Run server:

```bash
rails s -b 0.0.0.0 -p 3000
```

---

## **6.5 Node.js Projects**

### **Install Dependencies**

```bash
npm install
```

### **Run Development**

```bash
npm start   # or npm run dev
```

### **Run Build (React/Vue/Angular)**

```bash
npm run build
```

### **Run Backend in Production**

Using PM2:

```bash
npm install pm2 -g
pm2 start app.js
```

---

# **6.6 Running Projects as Linux System Services (Production Best Practice)**

Create a service file:

```bash
sudo nano /etc/systemd/system/myapp.service
```

Example for Python/Node/Java:

```
[Unit]
Description=My Application
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/opt/project
ExecStart=/usr/bin/python3 /opt/project/app.py   # change as required
Restart=always

[Install]
WantedBy=multi-user.target
```

Enable and run:

```bash
sudo systemctl daemon-reload
sudo systemctl enable myapp
sudo systemctl start myapp
```

Check logs:

```bash
journalctl -u myapp -f
```

---

# **6.7 Database Startup (If Required)**

### **MySQL / MariaDB**

```bash
sudo systemctl start mysql
sudo systemctl enable mysql
```

### **PostgreSQL**

```bash
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

---

# **6.8 Additional Tips for Running Projects**

✔ Use `tmux` or `screen` to keep apps running after SSH logout:

```bash
tmux new -s myapp
```

✔ Use `netstat` to check if your server is running:

```bash
sudo netstat -tulpn | grep :8000
```

✔ Check firewall if app not accessible:

```bash
sudo ufw allow 8000
```

---

Below is a **complete “Docker Commands + Docker Compose + Troubleshooting + How to Run” section** that you can append to your README.md.

If you want, I can **regenerate the full README including all updates** in one single file.

---

# **7. Docker & Docker Compose Guide (Windows + Linux)**

Even though your main migration does **not** use Docker, this section helps users run the same project using Docker if required — useful for local development, CI pipelines, or containerized deployments.

---

# **7.1 Install Docker**

## **A) Linux (Ubuntu / Debian)**

```bash
sudo apt update
sudo apt install ca-certificates curl gnupg -y

# Add Docker GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Add Docker repo
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

Enable Docker:

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

---

## **B) Red Hat / CentOS**

```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

---

## **C) Windows**

1. Download Docker Desktop:
   [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
2. Enable WSL 2
3. Reboot
4. Run Docker Desktop

---

# **7.2 Basic Docker Commands (Linux / Windows)**

## **Container Commands**

```bash
docker ps                    # Show running containers
docker ps -a                 # Show all containers
docker start <container>     # Start container
docker stop <container>      # Stop container
docker restart <container>   # Restart container
docker rm <container>        # Remove container
docker logs <container>      # View logs
docker exec -it <container> bash   # Enter container shell
```

---

## **Image Commands**

```bash
docker images                     # List images
docker pull ubuntu:latest         # Download image
docker build -t myapp .           # Build image from Dockerfile
docker rmi <image>                # Remove image
```

---

## **System Cleanup**

```bash
docker system prune -a
docker volume prune
docker network prune
```

---

# **7.3 Running Containers**

### **Run a simple container**

```bash
docker run -it ubuntu bash
```

### **Run with port mapping**

```bash
docker run -d -p 8000:8000 myapp
```

### **Run with environment variables**

```bash
docker run -d -p 8000:8000 -e DB_HOST=mysql -e DB_USER=root myapp
```

---

# **7.4 Docker Compose Commands**

### **Start services**

```bash
docker compose up
```

### **Start in detached mode**

```bash
docker compose up -d
```

### **Stop services**

```bash
docker compose down
```

### **Rebuild containers**

```bash
docker compose up --build
```

### **View logs**

```bash
docker compose logs -f
```

### **Run a command inside a service**

```bash
docker compose exec web bash
```

---

# **7.5 Example Docker Compose File**

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "8000:8000"
    environment:
      - APP_ENV=production
    depends_on:
      - db
  
  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: projectdb
    ports:
      - "3306:3306"
```

---

# **7.6 How to Run a Project with Docker**

### **1. Build image**

```bash
docker build -t myapp .
```

### **2. Run**

```bash
docker run -p 8000:8000 myapp
```

### **3. Or use Docker Compose**

```bash
docker compose up --build
```

---

# **7.7 Common Docker Troubleshooting**

---

## **1. Docker permission denied**

If you get:

```
Got permission denied while trying to connect to the Docker daemon socket
```

Fix:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## **2. Port already in use**

Error:

```
bind: address already in use
```

Find process:

```bash
sudo lsof -i :8000
sudo kill -9 <PID>
```

Or change port in `docker-compose.yml`.

---

## **3. Container exits immediately**

Check logs:

```bash
docker logs <container_name>
```

---

## **4. Build fails due to cache**

```bash
docker build --no-cache -t myapp .
```

---

## **5. Docker Desktop WSL error (Windows)**

Fix:

```powershell
wsl --shutdown
Restart-Service LxssManager
```

---

## **6. Docker Compose not found (Linux)**

```bash
sudo apt install docker-compose-plugin
docker compose version
```

---

## **7. Container cannot connect to database**

Ensure both are in **same network**:

```bash
docker network ls
docker network inspect <network>
```

Ensure DB is reachable:

```bash
docker exec -it web ping db
```

---

# **7.8 Docker Best Practices**

✔ Always use `.dockerignore` to reduce build size
✔ Keep Dockerfile clean with multi-stage builds
✔ Use named volumes for data

```yaml
volumes:
  db_data:
```

✔ Restart policy for services

```yaml
restart: always
```

✔ Keep services separated (web, db, cache, redis, etc.)

---



