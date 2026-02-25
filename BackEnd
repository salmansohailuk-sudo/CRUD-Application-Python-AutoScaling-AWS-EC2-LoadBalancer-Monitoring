🚀 Flask + MySQL User API

🚀 BACKEND INSTALLATION sudo yum update -y 
sudo yum update -y

sudo yum install python3-pip -y 

pip3 install flask pymysql gunicorn


📁 Project Structure
app/
│── app.py

Create app folder:

##BASH

mkdir app 
cd app 
vi app.py 


##🧩 BACKEND CODE - Copy and paste below scripts in app.py file and Replace RDS endpoint.
===============

from flask import Flask, request, jsonify
import pymysql
import os

app = Flask(__name__)

# -------------------------------
# Database Connection Function
# -------------------------------
def get_db_connection():
    return pymysql.connect(
        host="YOUR RDS ENDPOINT REPLACE",
        user="admin",
        password="Cloud123",
        database="testdb",
        cursorclass=pymysql.cursors.DictCursor,
        autocommit=True
    )
# -------------------------------
# Update Users
# -------------------------------


@app.route("/users/<int:id>", methods=["PUT"])
def update_user(id):
    data = request.get_json()
    name = data.get("name")
    email = data.get("email")

    conn = get_db_connection()
    cur = conn.cursor()

    cur.execute(
        "UPDATE users SET name=%s, email=%s WHERE id=%s",
        (name, email, id)
    )

    conn.close()
    return {"message": "User updated"}


# -------------------------------
# GET All Users
# -------------------------------
@app.route("/users", methods=["GET"])
def get_users():
    conn = get_db_connection()
    cur = conn.cursor()

    cur.execute("SELECT * FROM users")
    data = cur.fetchall()

    conn.close()
    return jsonify(data)

# -------------------------------
# POST Add User
# -------------------------------
@app.route("/users", methods=["POST"])
def add_user():
    data = request.get_json()

    name = data.get("name")
    email = data.get("email")

    if not name or not email:
        return {"error": "Missing name or email"}, 400

    conn = get_db_connection()
    cur = conn.cursor()

    cur.execute(
        "INSERT INTO users (name, email) VALUES (%s, %s)",
        (name, email)
    )

    conn.close()
    return {"message": "User added successfully"}, 201

# -------------------------------
# DELETE User
# -------------------------------
@app.route("/users/<int:id>", methods=["DELETE"])
def delete_user(id):
    conn = get_db_connection()
    cur = conn.cursor()

    cur.execute("DELETE FROM users WHERE id=%s", (id,))
    conn.close()

    return {"message": "User deleted successfully"}

# -------------------------------
# Run App
# -------------------------------
if __name__ == "__main__":
    app.run(debug=True)

##=========End of Code


#BASH

python3 app.py
Ctrl C

🚀 RUN On BACKEND Server 

#BASH
gunicorn -w 4 -b 0.0.0.0:8000 app:app


==============End
