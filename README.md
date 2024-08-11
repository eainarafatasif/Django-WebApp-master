# Django-WebApp       <img alt="GitHub" src="https://img.shields.io/github/license/smahesh29/Django-WebApp">


This project was done by me as a assignment for an internship.

<h2>Assignment Problem Statement:</h2>

<h4>Part 1:</h4>
<ol>
    <li>Create a web-app where a user can login.</li>
    <li>User can upload files.</li>
    <li>User can view his/her uploaded files.</li>
</ol>

<h4>Part 2:</h4>
<ol>
     <li>User can search and view profile of other users.</li>
     <li>They can share their uploaded files with any of those users.</li>
     <li>Users can see the shared files by other users also in uploaded files.</li>
</ol>

<h4>Additional Features:</h4>
<ol>
    <li>In users profile user can set his/her profile picture.</li>
    <li>Users can download other users uploaded files.</li>
    <li>The user can upload any type of files such as images, videos, text files and also different types of programs like python code, java code, etc.</li>
</ol>
    
<h2>Technologies Used:</h2>
<ul>
    <li>Python</li>
    <li>Django</li>
    <li>Bootstrap</li>
    <li>JavaScript</li>
</ul>
    
<h2>Additional Python Modules Required:</h2>
<ul>
    <li>Django</li>
    <li>django-crispy-forms</li>
    <li>Pillow</li>
</ul>
  
<h2>Note :</h2>

<b>The Secret_Key required for the execution and debugging of project is not removed from the project code. So you can use the project as your college mini-project or by using the project code you can build your own project.</b>

<h2>Installation:</h2>


To deploy your Django project from GitHub using Nginx, MySQL, and an SSL certificate on an Ubuntu server, follow these steps:

### Prerequisites
- An Ubuntu server with root or sudo access.
- A domain name pointing to your server's IP address.
- A GitHub repository containing your Django project.
- A virtual environment set up for your project.
  
### 1. **Update and Install Dependencies**
   ```bash
   sudo apt update && sudo apt upgrade -y
   sudo apt install python3-pip python3-dev libpq-dev nginx curl git
   sudo apt install mysql-server libmysqlclient-dev
   ```
Once all the packages are installed, you can start the Apache service and configure it to run on startup by entering the following commands:

```bash
 systemctl start Nginx
 systemctl enable Nginx

```
Now Allow the firewall
```bash
ufw allow 80
ufw allow 443

```

### 2. **Clone Your GitHub Repository**
   ```bash
   cd /var/www/html/
   sudo git clone https://github.com/unfoldadmin/django-unfold.git
   cd django-unfold
   ```

### 3. **Set Up Virtual Environment**
   ```bash
   sudo apt install python3-venv
   python3 -m venv projectenv
   source projectenv/bin/activate
   pip install django
   pip install mysqlclient
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

### 4. **Configure Django Settings**
- Update your `settings.py` to include your domain in the `ALLOWED_HOSTS`:
   ```python
   ALLOWED_HOSTS = ['yourdomain.com', 'www.yourdomain.com']
   ```
- Ensure your `DATABASES` setting points to your MySQL database.

### 5. **Set Up MySQL Database**
   ```bash
   sudo apt install mysql-server
   sudo mysql_secure_installation
   ```
   - Log in to MySQL and create a database and user:
     ```bash
     sudo mysql -u root -p
     ```
     ```sql
     CREATE DATABASE django_unfold;
     CREATE USER 'youruser'@'localhost' IDENTIFIED BY 'yourpassword';
     GRANT ALL PRIVILEGES ON django_unfold.* TO 'youruser'@'localhost';
     FLUSH PRIVILEGES;
     EXIT;
     ```
   - Update your Django `settings.py` to connect to the MySQL database.

  ```bash
  DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'your_db_name',
        'USER': 'your_db_user',
        'PASSWORD': 'your_db_password',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}

 ```

### 6. **Run Migrations and Collect Static Files**
   ```bash
   python manage.py makemigrations
   python manage.py migrate
   python manage.py collectstatic
   ```

### 7. **Set Up Gunicorn**
   ```bash
   pip install gunicorn
   gunicorn --workers 3 --bind unix:/var/www/html/django-unfold/gunicorn.sock django_unfold.wsgi:application
   ```

   - Create a systemd service file for Gunicorn:
     ```bash
     sudo nano /etc/systemd/system/gunicorn.service
     ```
     - Add the following:
       ```ini
       [Unit]
       Description=gunicorn daemon
       After=network.target

       [Service]
       User=www-data
       Group=www-data
       WorkingDirectory=/var/www/html/django-unfold
       ExecStart=/var/www/html/django-unfold/projectenv/bin/gunicorn \
                 --access-logfile - \
                 --workers 3 \
                 --bind unix:/var/www/html/django-unfold/gunicorn.sock \
                 django_unfold.wsgi:application

       [Install]
       WantedBy=multi-user.target
       ```
     - Enable and start the Gunicorn service:
       ```bash
       sudo systemctl start gunicorn
       sudo systemctl enable gunicorn
       ```

### 8. **Configure Nginx**
   - Create an Nginx server block:
     ```bash
     sudo nano /etc/nginx/sites-available/django_unfold
     ```
     - Add the following:
       ```nginx
       server {
           listen 80;
           server_name yourdomain.com www.yourdomain.com;

           location = /favicon.ico { access_log off; log_not_found off; }
           location /static/ {
               root /var/www/html/django-unfold;
           }

           location / {
               include proxy_params;
               proxy_pass http://unix:/var/www/html/django-unfold/gunicorn.sock;
           }
       }
       ```
   - Enable the configuration and restart Nginx:
     ```bash
     sudo ln -s /etc/nginx/sites-available/django_unfold /etc/nginx/sites-enabled
     sudo nginx -t
     sudo systemctl restart nginx
     ```

Ensure Correct Permissions for Static and Media Files
If you have static and media directories outside of the main project directory, set their ownership and permissions similarly:

```bash
sudo chown -R www-data:www-data /var/www/html/django-unfold/
sudo chmod -R 755 /var/www/html/django-unfold/

```

### 9. **Obtain an SSL Certificate**
   - Install Certbot:
     ```bash
     sudo apt install certbot python3-certbot-nginx
     ```
   - Obtain and install the SSL certificate:
     ```bash
     sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
     ```
   - Follow the prompts to complete the SSL installation. Certbot will automatically configure your Nginx server block to use SSL.

### 10. **Testing**
   - Visit `https://yourdomain.com` to ensure everything is working correctly.

### 11. **Set Up Automatic Renewals for SSL**
   ```bash
   sudo systemctl status certbot.timer
   ```

This setup will get your Django project deployed with Nginx, MySQL, and an SSL certificate on Ubuntu.

    
   In your web browser enter the address : http://localhost:8000 or http://127.0.0.1:8000/

# Working:
[![Watch the video](https://img.youtube.com/vi/qIK-vfTig6c/0.jpg)](https://youtu.be/qIK-vfTig6c)

# Screenshots : 
<img src="Screenshots/New Tab - Google Chrome 03-12-2019 19_14_36.png" height="400" width="800">
<img src="Screenshots/New Tab - Google Chrome 03-12-2019 19_14_51.png" height="400" width="800">
<img src="Screenshots/New Tab - Google Chrome 03-12-2019 19_14_44.png" height="400" width="800">
<img src="Screenshots/New Tab - Google Chrome 03-12-2019 19_15_47.png" height="400" width="800">
<img src="Screenshots/New Tab - Google Chrome 03-12-2019 19_16_14.png" height="400" width="800">
<img src="Screenshots/Django WebApp - Google Chrome 04-12-2019 13_41_50.png" height="400" width="800">
<img src="Screenshots/Django WebApp - Google Chrome 03-12-2019 20_48_45.png" height="400" width="800">

