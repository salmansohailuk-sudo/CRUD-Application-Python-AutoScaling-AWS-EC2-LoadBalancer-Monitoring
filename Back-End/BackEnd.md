🚀 Flask + MySQL User API

🚀 BACKEND INSTALLATION 

<script>
sudo yum update -y

sudo yum install python3-pip -y 

pip3 install flask pymysql gunicorn
</script>

📁 Project Structure
app/
│── app.py

Create app folder:

##BASH
<script>
mkdir app

cd app 

vi app.py 


python3 app.py
</script>
#BASH

gunicorn -w 4 -b 0.0.0.0:8000 app:app
