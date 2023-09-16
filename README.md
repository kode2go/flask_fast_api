# flask_api_service_setup

Setup simple flask api that reads in csv file setup with nginx on ubuntu vm

Prerequisites:

An Ubuntu Virtual Machine (VM) with SSH access.
Basic knowledge of the Linux command line.
Step 1: Update and Upgrade
SSH into your Ubuntu VM and make sure it's up-to-date:

```
sudo apt update
sudo apt upgrade -y
```

Step 2: Install Python and Flask
Install Python and Flask if they are not already installed:

```
sudo apt install python3 python3-pip
pip3 install Flask
```

Step 3: Create Your Flask Application
Create a directory for your Flask app and navigate to it:

```
mkdir flask_csv_api
cd flask_csv_api
```

Inside the directory, create a Python script, for example, app.py, for your Flask application:

```
from flask import Flask, jsonify

app = Flask(__name__)

# Replace 'data.csv' with your actual CSV file name and path
CSV_FILE = 'data.csv'

@app.route('/data', methods=['GET'])
def get_data():
    try:
        with open(CSV_FILE, 'r') as file:
            data = file.read()
        return jsonify({"data": data.splitlines()})
    except FileNotFoundError:
        return jsonify({"error": "CSV file not found"}), 404

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

Make sure to replace 'data.csv' with the actual path to your CSV file.

Step 4: Test Your Flask App
Run your Flask application to ensure it's working correctly:

```
python3 app.py
```

Visit http://<your-vm-ip>:5000/data in your web browser to see if the API responds with your CSV data.

Step 5: Install and Configure Nginx
Install Nginx:

```
sudo apt install nginx
```

Create a new Nginx configuration file for your Flask app:

```
sudo nano /etc/nginx/sites-available/flask_csv_api
```

Add the following configuration, replacing <your-domain-or-ip> with your server's IP address or domain name:

```
server {
    listen 80;
    server_name <your-domain-or-ip>;

    location / {
        include proxy_params;
        proxy_pass http://127.0.0.1:5000;
    }
}
```

Create a symbolic link to enable the configuration:

```
sudo ln -s /etc/nginx/sites-available/flask_csv_api /etc/nginx/sites-enabled/
```

Test the Nginx configuration and restart Nginx:

```
sudo nginx -t
sudo systemctl restart nginx
```

Step 6: Access Your API
You should now be able to access your Flask API through Nginx at http://<your-domain-or-ip>/data.

That's it! You have set up a simple Flask API that reads a CSV file and configured Nginx to serve it on your Ubuntu VM.

## Dockerize

```
# Use an official Python runtime as a parent image
FROM python:3.8-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV CSV_FILE=data.csv

# Run app.py when the container launches
CMD ["python", "app.py"]
```
