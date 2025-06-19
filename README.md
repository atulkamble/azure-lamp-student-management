## ☁️ Azure VM LAMP Server Setup

**Deploy and run your Student Management System on a Linux VM with Apache, MySQL, and PHP**

---

## ✅ Prerequisites

* **Azure Subscription**
* **Azure CLI installed locally** (optional if using portal)
* SSH key pair for authentication

---

## ✅ Create Azure VM (Ubuntu 22.04)

# log in to azure
```bash
az login
```
# Create resource group
```
az group create --name LAMP-ResourceGroup --location eastus
```
# Create VM (Ubuntu 24.04)
```
az vm create \
  --resource-group LAMP-ResourceGroup \
  --name LampVM \
  --image Ubuntu2404 \
  --admin-username atul \
  --generate-ssh-keys \
  --size Standard_B1s
```

**Open ports for HTTP, HTTPS, and MySQL**

```
az vm open-port --resource-group LAMP-ResourceGroup --name LampVM --port 80 --priority 1010
az vm open-port --resource-group LAMP-ResourceGroup --name LampVM --port 443 --priority 1020
az vm open-port --resource-group LAMP-ResourceGroup --name LampVM --port 3306 --priority 1030

```

---

## ✅ SSH into VM

```bash
ssh azureuser@<Public-IP>
```

---

## ✅ Install LAMP Stack

### Install Apache

```bash
sudo apt update
sudo apt install apache2 -y
sudo systemctl enable apache2
sudo systemctl start apache2
```

### Install MySQL

```bash
sudo apt install mysql-server -y
sudo systemctl enable mysql
sudo systemctl start mysql
```

**Secure MySQL (optional)**

```bash
sudo mysql_secure_installation
```

### Install PHP

```bash
sudo apt install php libapache2-mod-php php-mysql -y
sudo systemctl restart apache2
```

---

## ✅ Deploy Student Management System

### Install Git

```bash
sudo apt install git -y
```

### Clone your repo into Apache root

```bash
cd /var/www/html
sudo git clone https://github.com/atulkamble/xampp-student-management-system.git student_management
sudo chown -R www-data:www-data /var/www/html/student_management
```

---

## ✅ Setup MySQL Database

**Login to MySQL**

```bash
sudo mysql
```

**Create Database & Table**

```sql
CREATE DATABASE student_db;
USE student_db;

CREATE TABLE students (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);
EXIT;
```

---

## ✅ Configure PHP App

Edit `db.php` in the project folder:

```php
<?php
$conn = mysqli_connect("localhost", "root", "", "student_db");
if (!$conn) {
    die("Connection failed: " . mysqli_connect_error());
}
?>
```

*If you’ve set a MySQL password for root, add it in `mysqli_connect()` as the 4th parameter.*

---

## ✅ Test Web Application

In your browser:

```
http://<Public-IP>/student_management/
```

✅ You should see your Student Management System running on Azure VM with LAMP.

---

## 📦 Optional: Enable Apache mod\_rewrite (if URLs need cleaning)

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

---

## 🎉 Done!

Now your project runs on a **production-like cloud Linux LAMP environment** — no XAMPP required!
