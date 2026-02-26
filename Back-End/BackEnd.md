🚀 Flask + MySQL User API

🚀 BACKEND INSTALLATION 

This renders nicely with syntax highlighting.

---

# 3️⃣ Inline Code

For small snippets **inside a sentence**, use single backticks:

```markdown
Use `git commit -m "message"` to commit changes.


<code>

sudo yum update -y

sudo yum install python3-pip -y 

pip3 install flask pymysql gunicorn
</code>

📁 Project Structure
app/
│── app.py

Create app folder:

##BASH
<code>

mkdir app

cd app 

vi app.py 


python3 app.py
</code>
#BASH

gunicorn -w 4 -b 0.0.0.0:8000 app:app
