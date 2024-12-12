# Introduction
This is a dynamic web application that fetches weather data for a specified city using the OpenWeatherMap API. Built with React for the frontend and Python Flask for the backend. Below are the deployment steps for Ubuntu systems:
## Prerequisites
1. **AWS Account:** An active AWS account to deploy the app.
2. **GitHub Repository:** Your project hosted on GitHub.
3. **OpenWeatherMap API Key:** Obtain an API key from OpenWeatherMap.
4. **Local Development Tools:**
   - Python 3.x
   - Node.js and npm
   - Git
  
## Steps to Deploy on AWS EC2
**1. Launch an AWS EC2 Instance**
  1. Log in to the AWS Management Console.
  2. Go to the EC2 Dashboard and click Launch Instances.
  3. Choose the Amazon Machine Image (AMI): Ubuntu 22.04 LTS.
  4. Select an instance type (e.g., t2.micro for free tier eligibility).
  5. Configure the security group to allow:
    * Port 22 (SSH)
    * Port 80 (HTTP)
    * Port 5000 (Flask backend)
  6. Launch the instance and download the key pair file (e.g., ec2-key.pem).

<img src="https://github.com/user-attachments/assets/a20fcdc9-3b16-4f89-89c1-1d4e56c465b9" alt="Screenshot" width="500">

<img src="https://github.com/user-attachments/assets/2ceb49f4-0e84-4578-85fb-bd6390de7aef" alt="Screenshot" width="500">

<img src="https://github.com/user-attachments/assets/d8ae640a-bdd4-46b2-a2f8-255955daba76" alt="Screenshot" width="500">


**2. Connect to the EC2 Instance**
  1. Use SSH to connect to your instance:
  ```
chmod 400 ec2-key.pem
ssh -i ec2-key.pem ubuntu@<EC2_PUBLIC_IP>
```
**3. Install Dependencies on EC2**
  1. Update the system:
```
sudo apt update && sudo apt upgrade -y
```
<img width="500" alt="Screenshot 2024-12-11 at 11 25 37 AM" src="https://github.com/user-attachments/assets/aedb1e1b-2058-48be-b89c-3339fd5d7070" />

  2. Install Python, Node.js, and Git:
```
sudo apt install python3-pip python3-venv nodejs npm git -y
```
<img width="500" alt="Screenshot 2024-12-11 at 11 26 55 AM" src="https://github.com/user-attachments/assets/3b06d48f-9e51-4cc9-9bd1-51c70fadda6c" />

**4. Clone the GitHub Repository**
  1. Clone this repo - https://github.com/zippyzap2/weather-app.git
```
git clone https://github.com/zippyzap2/weather-app.git
cd <REPO_FOLDER>
```
<img width="500" alt="Screenshot 2024-12-11 at 2 19 15 PM" src="https://github.com/user-attachments/assets/aea24972-96a9-4a1e-be80-cc6c8a7a2e8e" />

**5. Set Up the Backend**
  1. Navigate to the backend folder:
```
cd backend
```
  2. Create a .env file and add your API key:
```
sudo vi .env
WEATHER_API_KEY=your_api_key_here
```
  3. Create a virtual environment and install dependencies:
```
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```
<img width="500" alt="Screenshot 2024-12-11 at 2 27 02 PM" src="https://github.com/user-attachments/assets/3c6b619b-18aa-438c-8757-09ce48cebfc4" />

  4. Update app.py to bind Flask to all interfaces:
```
app.run(host='0.0.0.0', debug=True)
```
  5. Start the Flask backend:
```
python app.py
```
<img width="500" alt="Screenshot 2024-12-11 at 2 48 04 PM" src="https://github.com/user-attachments/assets/4d8208f5-a2f7-42fd-9033-c368b4b184a9" />

**6. Set Up the Frontend**
  1. Navigate to the frontend folder:
```
cd frontend
```
  2. Install dependencies
```
npm install
```
<img width="500" alt="Screenshot 2024-12-11 at 2 31 48 PM" src="https://github.com/user-attachments/assets/260591bd-abd3-4858-8085-0ad1dfbb38a8" />

  3. Update the API endpoint in App.js:
     - Replace http://localhost:5000 with http://<EC2_PUBLIC_IP>:5000
  4. Build the React app:
```
npm run build
```
<img width="500" alt="Screenshot 2024-12-11 at 2 35 45 PM" src="https://github.com/user-attachments/assets/2ab8ae00-02e2-4d43-87f9-2764aef4ca5c" />

  5. Serve the React app:
```
sudo npm install -g serve
sudo serve -s build -l 80
```
<img width="500" alt="Screenshot 2024-12-11 at 2 36 34 PM" src="https://github.com/user-attachments/assets/55887a50-8d25-4b55-8e4a-a0568ee3fe0c" />

<img width="500" alt="Screenshot 2024-12-11 at 2 44 30 PM" src="https://github.com/user-attachments/assets/c8f91b73-dd6b-4a3f-b466-91fb22743a51" />


**7. Configure Backend and Frontend to Run Simultaneously**
  1. Use PM2 to manage processes:
```
sudo npm install -g pm2
sudo pm2 start python -- app.py
sudo pm2 start "serve -s build -l 80" --name frontend
```
<img width="500" alt="Screenshot 2024-12-11 at 2 55 22 PM" src="https://github.com/user-attachments/assets/af2078f0-16c1-46c5-ae3c-daed63e5b152" />

<img width="500" alt="Screenshot 2024-12-11 at 2 56 03 PM" src="https://github.com/user-attachments/assets/60c3854c-8629-4890-b88a-d335914ff16d" />

<img width="500" alt="Screenshot 2024-12-11 at 3 00 43 PM" src="https://github.com/user-attachments/assets/e29a2fcc-0ee2-4c07-ac19-bd20b948c135" />

  2. Save PM2 processes:
```
pm2 save
pm2 startup
```
<img width="500" alt="Screenshot 2024-12-11 at 2 58 33 PM" src="https://github.com/user-attachments/assets/108739dc-f3ad-4a97-a8a5-26de945c9a14" />

**8. Optional - Use a Reverse Proxy (e.g., Nginx)**
  1. Install Nginx:
```
sudo apt install nginx -y
```
  2. Configure Nginx to proxy traffic to your React app:
  - Open Nginx configuration file
    ```
    sudo nano /etc/nginx/sites-available/default
    ```
  - Replace the content
    ```
    server {
       listen 80;

       root /home/ubuntu/weather-app/frontend/build;
       index index.html;

       location / {
           try_files $uri /index.html;
       }
    }

    ```
  - Test and reload Nginx:
    ```
    sudo nginx -t
    sudo systemctl restart nginx
    ```
   - If issue loding application ensure correct permissions
     ```
     sudo chmod 755 /home/ubuntu/weather-app/frontend
     sudo chmod 755 /home/ubuntu/weather-app
     sudo chmod 755 /home/ubuntu
     sudo systemctl restart nginx

     ```
   - Enable Nginx to Start on Boot
     ```
     sudo systemctl enable nginx
     ```
**9. Test and Secure the Deployment**
  1. Test your app:
     - Access your app in the browser via http://<EC2_PUBLIC_IP>
     

https://github.com/user-attachments/assets/82b7ce3a-2af5-478b-bb61-7e768e5562a4


  2. Secure the server:
     - Install and configure a firewall:
       ```
       sudo ufw enable
       sudo ufw allow 'Nginx Full'
       sudo ufw allow 5000
       sudo ufw allow 80
       ```
**10. Monitor logs for debugging:**
```
# check pm2 logs
pm2 logs

# to identify the process occupying port 80
sudo lsof -i :80

# Ensure the Firewall is Open
sudo ufw status
sudo iptables -L

# Allow the required ports if necessary
sudo ufw allow 80
sudo ufw allow 5000
sudo ufw reload

# Verify Your App is Listening on the Right Ports
sudo netstat -tuln
sudo netstat -tuln | grep 80
sudo netstat -tuln | grep 5000

# Nginx logs to understand the error
sudo tail -f /var/log/nginx/error.log

# Test Nginx Configuration
sudo nginx -t

# Ensure npm and node are installed correctly and functioning
node -v
npm -v

# EC2 instance connection issue
chmod 400 <key.pem>

# Check system resources (CPU, memory, storage):
free -h
df -h
top

# Verify Flask is running
ps aux | grep app.py

# Check Flask logs
tail -f backend.log

```



