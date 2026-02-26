# 🐍 Flask + MySQL User API

A simple **REST API** built with **Flask** and **MySQL**, designed for user management.

---

## 🚀 Backend Installation

### 1️⃣ Update System

```bash
sudo yum update -y
```

### 2️⃣ Install Python3 and Pip

```bash
sudo yum install python3-pip -y
```

### 3️⃣ Install Required Python Packages

```bash
pip3 install flask pymysql gunicorn
```

---

## 📁 Project Structure

```
app/
├── app.py
```

---

## 🏗 Create App Folder

```bash
mkdir app
cd app
vi app.py
```

> Add your Flask code inside `app.py`.

---

## ▶️ Run the App Locally

```bash
python3 app.py
```

---

## 🚀 Run with Gunicorn (Production-Style)

```bash
gunicorn -w 4 -b 0.0.0.0:8000 app:app
```

- `-w 4` → 4 worker processes  
- `-b 0.0.0.0:8000` → bind to port 8000, accessible from all interfaces  

---

## 📝 Notes

- Make sure MySQL server is running and accessible  
- Configure your database credentials in `app.py`  
- For production, consider using **Nginx + Gunicorn** setup  

---

## 🔗 References

- [Flask Documentation](https://flask.palletsprojects.com/)  
- [PyMySQL Documentation](https://pymysql.readthedocs.io/)  
- [Gunicorn Documentation](https://gunicorn.org/)
