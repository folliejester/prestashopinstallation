# Prestashop Installation Guide v1.7.8.11 (Copy & Paste)




## Installation

### Step 1: Update Your System

First, update your package list and upgrade existing packages:

```bash
    sudo apt update
    sudo apt upgrade -y

```

### Step 2: Install Apache

Install the Apache web server:
```bash
    sudo apt install apache2 -y
```
Enable Apache to start on boot and start the service:

```bash
    sudo systemctl enable apache2
    sudo systemctl start apache2

```
### Step 3: Install Required PHP and Extensions

Add the PHP repository and install PHP 7.4 along with necessary extensions:
```bash
    sudo add-apt-repository ppa:ondrej/php -y
    sudo apt update
    sudo apt install php7.4 php7.4-mysql php7.4-curl php7.4-json php7.4-gd php7.4-mbstring php7.4-xml php7.4-zip php7.4-intl php7.4-gd php7.4-intl -y
```
### Step 4: Install MySQL Client

Since you will be connecting to RDS, install the MySQL client:
```bash
    sudo apt install mysql-client -y
```
### Step 5: Install Composer

Install Composer, a dependency manager for PHP:
```bash
    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php -r "if (hash_file('sha384', 'composer-setup.php') === '3e3dc1e4b0f927c530a65e6bcb64a83d64fda2368b6f36d7c69c3bcbbf22f1398fd62245ecf83e0491188cd571b1e9d2d') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
    php composer-setup.php --install-dir=/usr/local/bin --filename=composer
    php -r "unlink('composer-setup.php');"
```
### Step 6: Configure PHP Settings

Edit the PHP configuration file to set necessary values:
```bash
    sudo nano /etc/php/7.4/apache2/php.ini
```
Make the following changes:


| Parameter | Value     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `memory_limit`      | `256M` | Max Ram Usage |
| `upload_max_filesize`      | `16M` | Max upload file size limit |
| `post_max_size`      | `16M` | Max post size limit |
| `max_execution_time`      | `300` | Timeout |
| `date.timezone`      | `'Your/Timezone'` | replace with your actual timezone, e.g., Asia/Kolkata |

After editing, restart Apache:

```bash
    sudo systemctl restart apache2
```
### Step 7: Set Up RDS

- Create a new RDS instance in the AWS console with MySQL.
- Note down the endpoint, database name, username, and password.

### Step 8: Download PrestaShop

Download PrestaShop 1.7.8.11:
```bash
    cd /var/www/html
    wget https://download.prestashop.com/download/releases/prestashop_1.7.8.11.zip
    apt install unzip
    unzip prestashop_1.7.8.11.zip
    rm -rf prestashop prestashop_1.7.8.11.zip

```
### Step 9: Set Permissions

Set proper permissions for the PrestaShop directory:
```bash
    sudo chown -R www-data:www-data /var/www/html
    sudo chmod -R 755 /var/www/html
```
### Step 10: Configure Apache for PrestaShop

Create a new Apache configuration file:
```bash
    sudo nano /etc/apache2/sites-available/prestashop.conf
```
Add the following configuration:
```bash
    <VirtualHost *:80>
        ServerName yourdomain.com
        DocumentRoot /var/www/html

        <Directory /var/www/html>
             AllowOverride All
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/prestashop-error.log
        CustomLog ${APACHE_LOG_DIR}/prestashop-access.log combined
    </VirtualHost>
```
Enable the site and rewrite module:
```bash
    sudo a2ensite prestashop.conf
    sudo a2enmod rewrite
    sudo systemctl restart apache2

```
### Step 11: Install PrestaShop

Now navigate to your domain in a web browser:
```bash
    yourdomain.com
```
Follow the on-screen instructions to complete the installation, entering your RDS details when prompted for database information.

### Step 12: Clean Up

After installation, delete the install folder for security:
```bash
    rm -rf /var/www/html/install
```
### Step 13: Secure Your Installation

It’s essential to configure additional security measures, such as enabling HTTPS and setting up firewalls.

### Step 14: Disable check for cookie ip (Optional)

Only do this step if you are being logged out of the admin immediately after logging in:

Here’s a complete example assuming your database endpoint is mydb.abcdefg123.us-west-2.rds.amazonaws.com, your username is admin, and the database name is mydatabase:

```bash
    mysql -h mydb.abcdefg123.us-west-2.rds.amazonaws.com -u admin -p
```

After entering your password:

```bash
    USE mydatabase;
    UPDATE ps_configuration SET value = 0 WHERE name = 'PS_COOKIE_CHECKIP';

```
