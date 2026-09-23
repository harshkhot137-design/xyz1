# xyz1
import sqlite3
from flask import Flask, request

app = Flask(__name__)

@app.route('/login', methods=['POST'])
def login():
    # ❌ CRITICAL VULNERABILITY: Raw string formatting for SQL queries
    # This allows a hacker to bypass authentication using SQL Injection.
    username = request.form.get('username')
    password = request.form.get('password')
    
    query = f"SELECT * FROM users WHERE username = '{username}' AND password = '{password}'"
    
    conn = sqlite3.connect('database.db')
    cursor = conn.cursor()
    cursor.execute(query) # Snyk Code (SAST) will flag this exact line
    
    user = cursor.fetchone()
    if user:
        return "Welcome back!"
    return "Invalid credentials", 401

if __name__ == '__main__':
    app.run(port=5000)
