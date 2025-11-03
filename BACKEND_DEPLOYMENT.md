i hope u are connected to the EC2 instance via SSH:

Backend Deployment README (Node.js + PM2 + NGINX + Env + HTTP/HTTPS + Firewall)

Step 1: Update server packages

sudo apt update

sudo apt upgrade -y





Meaning:



Updates package list (apt update) and upgrades installed packages (apt upgrade).



Step 2: Install Node.js 18 and build tools

curl -sL https://deb.nodesource.com/setup\_18.x | sudo -E bash -

sudo apt install -y nodejs build-essential

node -v

npm -v





Meaning:



Installs Node.js, npm, and build tools.



Step 3: Install PM2 globally

sudo npm install -g pm2





Meaning:



PM2 keeps your Node.js app running in the background and can restart it automatically on crashes or server reboot.



Step 4: Clone your project and install dependencies

git clone https://github.com/yourusername/your-ecommerce-backend.git

cd your-ecommerce-backend

npm install





Meaning:



Clones your GitHub repo and installs all dependencies.



Step 5: Setup environment variables

nano .env





Example:



PORT=5000

DB\_URL=mongodb+srv://username:password@cluster0.mongodb.net/mydb

JWT\_SECRET=your\_jwt\_secret





Meaning:



Stores sensitive information outside the code.



Accessed via process.env.PORT, etc.



Make sure dotenv package is installed and required in server.js:



require('dotenv').config();



Step 6: Start app with PM2

pm2 start src/server.js --name ecommerce-backend

pm2 save

pm2 startup ubuntu

pm2 status





Meaning:



Starts your backend in the background with PM2.



Saves process list for automatic restart after reboot.



Step 7: Setup firewall (UFW)

sudo ufw enable

sudo ufw allow ssh

sudo ufw allow http

sudo ufw allow https

sudo ufw status





Meaning:



Enables the firewall (ufw enable).



Allows:



SSH (port 22) → to connect to EC2



HTTP (port 80) → web traffic



HTTPS (port 443) → secure web traffic



ufw status shows which ports are allowed.



This protects your server by only allowing necessary ports.



Step 8: Install and configure NGINX

sudo apt install -y nginx

sudo systemctl enable nginx

sudo systemctl start nginx

sudo systemctl status nginx





Meaning:



Installs NGINX and ensures it starts automatically.



Step 9: Configure NGINX reverse proxy

sudo nano /etc/nginx/sites-available/default





Replace with:



server {

&nbsp;   listen 80;

&nbsp;   server\_name yourdomain.com www.yourdomain.com;



&nbsp;   location / {

&nbsp;       proxy\_pass http://localhost:5000;  # Node.js backend port

&nbsp;       proxy\_http\_version 1.1;

&nbsp;       proxy\_set\_header Upgrade $http\_upgrade;

&nbsp;       proxy\_set\_header Connection 'upgrade';

&nbsp;       proxy\_set\_header Host $host;

&nbsp;       proxy\_cache\_bypass $http\_upgrade;

&nbsp;   }



&nbsp;   server\_tokens off;

}





Meaning:



Forwards requests from NGINX to your Node.js app.



Handles WebSocket upgrades.



Hides NGINX version for security.



Step 10: Test and restart NGINX

sudo nginx -t

sudo systemctl restart nginx





Meaning:



Validates NGINX config and applies changes.



Step 11: Add HTTPS with Let’s Encrypt (Optional)

sudo apt install -y certbot python3-certbot-nginx

sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

sudo certbot renew --dry-run





Meaning:



Installs Certbot and issues free SSL certificates.



Configures HTTPS automatically in NGINX.



Step 12: Updating after code changes

cd ~/your-ecommerce-backend

git pull origin main

npm install       # if dependencies changed

pm2 reload ecommerce-backend





Meaning:



Pulls latest code from GitHub.



Installs new dependencies.



Reloads PM2 app without downtime.







Backend Deployment (Node.js + PM2 + NGINX)

Step 1: Update server packages

sudo apt update





Meaning:



Updates the list of available packages and their versions from repositories.



Ensures you install the latest versions of software.



Step 2: Install Node.js 18 and build tools

curl -sL https://deb.nodesource.com/setup\_18.x | sudo -E bash -

sudo apt install -y nodejs build-essential





Meaning:



First command adds Node.js 18 repository to your system.



Second installs Node.js and npm (Node package manager), and build-essential which is needed for compiling some npm packages.



Step 3: Install PM2 globally

sudo npm install -g pm2





Meaning:



PM2 is a process manager for Node.js apps.



It keeps the app running in the background, restarts it if it crashes, and can auto-start on system boot.



Step 4: Clone your project and install dependencies

git clone https://github.com/yourusername/your-ecommerce-backend.git

cd your-ecommerce-backend

npm install





Meaning:



Clones the GitHub repo to your server.



Installs all Node.js dependencies listed in package.json.



Step 5: Start the backend app with PM2

pm2 start src/server.js --name ecommerce-backend

pm2 save

pm2 startup ubuntu





Meaning:



pm2 start → Starts the app and assigns a process name.



pm2 save → Saves the current PM2 process list so it can be restored on reboot.



pm2 startup ubuntu → Generates a system startup script so PM2 automatically starts on system boot.



Step 6: Install and configure NGINX

sudo apt install -y nginx

sudo systemctl enable nginx

sudo systemctl start nginx





Meaning:



Installs NGINX (web server / reverse proxy).



enable → Starts NGINX automatically on boot.



start → Starts NGINX immediately.



Step 7: Configure NGINX reverse proxy

sudo nano /etc/nginx/sites-available/default





Replace contents with:



server {

&nbsp;   listen 80;

&nbsp;   server\_name \_;



&nbsp;   location / {

&nbsp;       proxy\_pass http://localhost:5000;  # Node.js backend port

&nbsp;       proxy\_http\_version 1.1;

&nbsp;       proxy\_set\_header Upgrade $http\_upgrade;

&nbsp;       proxy\_set\_header Connection 'upgrade';

&nbsp;       proxy\_set\_header Host $host;

&nbsp;       proxy\_cache\_bypass $http\_upgrade;

&nbsp;   }



&nbsp;   server\_tokens off;

}





Meaning:



Forwards HTTP requests coming to your server on port 80 to Node.js app on port 5000.



Adds headers to support WebSockets and proper host handling.



Hides NGINX version for security.



Step 8: Test and reload NGINX

sudo nginx -t

sudo systemctl restart nginx





Meaning:



nginx -t → Checks NGINX configuration for syntax errors.



restart → Applies the new configuration.

