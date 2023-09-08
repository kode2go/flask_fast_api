# FAST API Setup

To set up FastAPI on a virtual machine (VM) to read CSV data and display an API route, you can follow these steps:

Access Your VM: Log in to your virtual machine. You can use SSH or any other method to access your VM.

Install Python and Virtual Environment (if not already installed):

```
sudo apt update
sudo apt upgrade
sudo apt install python3 python3-pip
sudo apt install python3-venv  # Install virtual environment
```

Create a Directory for Your Project:

```
mkdir fastapi_csv_project
cd fastapi_csv_project
```

Set Up a Virtual Environment and Activate It:

```
python3 -m venv venv
source venv/bin/activate
```

Install FastAPI and Uvicorn:

```
pip install fastapi uvicorn
pip install pandas
```

Create a CSV Data File: Upload your CSV data file to the VM or create a sample CSV file. For this example, let's create a sample CSV file called data.csv with some sample data.

Create a FastAPI App:

Create a Python script (e.g., main.py) to set up your FastAPI app:

```
from fastapi import FastAPI
import pandas as pd

from fastapi.openapi.models import Info

app = FastAPI(
    title="Your API Title",
    description="Your API description",
    version="1.0",
    openapi_url="/api/v1/openapi.json",  # Change this URL if needed
    docs_url="/api/docs",  # URL for Swagger UI
    redoc_url="/api/redoc",  # URL for ReDoc
)

@app.get("/")
async def read_root():
    return {"message": "Welcome to FastAPI!"}

@app.get("/data")
async def read_data():
    df = pd.read_csv("data.csv")  # Load CSV data
    return df.to_dict(orient="records")
```

Install Nginx and create a new Nginx server block configuration file for your FastAPI application. 
```
sudo apt install nginx
```

You can create a new file in the /etc/nginx/sites-available directory:

```
sudo nano /etc/nginx/sites-available/fastapi
```

Add the following:

```
server {
    listen 80;
    server_name x.x.x.x;

    location / {
        include proxy_params;
        proxy_pass http://127.0.0.1:8000;  # Assuming your FastAPI app is running on loca>
    }

}
```

Link:

```
sudo ln -s /etc/nginx/sites-available/fastapi /etc/nginx/sites-enabled/
```

Test the Nginx configuration and restart Nginx:

```
sudo nginx -t
sudo systemctl restart nginx
```

Run (this runs in the background:

```
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

Access:

```
http://x.x.x.x/
http://x.x.x.x/api/docs
http://x.x.x.x/api/v1/openapi.json"
http://x.x.x.x/data
```
