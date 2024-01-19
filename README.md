 **# Deploying FastAPI with Nginx, Uvicorn, Gunicorn, and PM2 on Ubuntu**

**## Table of Contents**

- Prerequisites: #prerequisites
- Installation: #installation
- Setting Up FastAPI Application: #setting-up-fastapi-application
- Running FastAPI with Gunicorn: #running-fastapi-with-gunicorn
- Managing with PM2: #managing-with-pm2
- Configuring Nginx as a Reverse Proxy: #configuring-nginx-as-a-reverse-proxy
- Testing the Deployment: #testing-the-deployment
- Additional Notes: #additional-notes

**## Prerequisites**

- An Ubuntu server with SSH access
- Python 3.6 or newer installed
- A non-root user with sudo privileges

**## Installation**

1. **Update Package Lists:**

   ```bash
   sudo apt update
   ```

2. **Install Python Dependencies:**

   ```bash
   sudo apt install python3-pip python3-venv nginx
   ```

3. **Create a Virtual Environment (Recommended):**

   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```

4. **Install FastAPI, Uvicorn, and Gunicorn:**

   ```bash
   pip install fastapi uvicorn[standard] gunicorn
   ```

5. **Install PM2 (Process Manager):**

   ```bash
   sudo npm install -g pm2
   ```

**## Setting Up FastAPI Application**

1. **Create a Project Directory:**

   ```bash
   mkdir my_fastapi_app
   cd my_fastapi_app
   ```

2. **Create a Main File (main.py):**

   ```python
   from fastapi import FastAPI

   app = FastAPI()

   @app.get("/")
   def root():
       return {"message": "Hello World!"}
   ```

**## Running FastAPI with Gunicorn**

1. **Start the Application:**

   ```bash
   gunicorn -w 4 -k uvicorn.workers.UvicornWorker main:app
   ```

**## Managing with PM2**

1. **Start with PM2:**

   ```bash
   pm2 start "gunicorn -w 4 -k uvicorn.workers.UvicornWorker main:app" --name my_fastapi_app
   ```

2. **Manage PM2 Processes:**

   - List processes: `pm2 list`
   - Stop a process: `pm2 stop my_fastapi_app`
   - Restart a process: `pm2 restart my_fastapi_app`

**## Configuring Nginx as a Reverse Proxy**

1. **Create Nginx Configuration File:**

   ```bash
   sudo nano /etc/nginx/sites-available/my_fastapi_app.conf
   ```

2. **Paste the Following Configuration:**

   ```nginx
   server {
       listen 80;
       server_name your_domain_or_IP;

       location / {
           proxy_pass http://localhost:8000;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'upgrade';
           proxy_set_header Host $host;
           proxy_cache_bypass $http_upgrade;
       }
   }
   ```

3. **Enable the Configuration:**

   ```bash
   sudo ln -s /etc/nginx/sites-available/my_fastapi_app.conf /etc/nginx/sites-enabled/
   ```

4. **Test Nginx Configuration:**

   ```bash
   sudo nginx -t
   ```

5. **Restart Nginx:**

   ```bash
   sudo service nginx restart
   ```

**## Testing the Deployment**

1. Access your server's IP address or domain name in a web browser. You should see the "Hello World!" message from your FastAPI app.

**##
