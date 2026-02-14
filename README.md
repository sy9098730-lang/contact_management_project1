# contact_management_proje
 # contact_management_project

rom cryptography.fernet import Fernet
import hashlib
import _sqlite3

KEY = Fernet.generate_key()
cipher = Fernet(KEY)

conn = _sqlite3.connect("contact.db")
cursor = conn.cursor()

cursor.execute("""
CREATE TABLE IF NOT EXISTS users (
	username TEXT PRIMARY KEY,
	password TEXT
	)
""")

cursor.execute("""
CREATE TABLE IF NOT EXISTS contacts (
	id INTEGER PRIMARY KEY AUTOINCREMENT,
	owner TEXT,
	name TEXT,
	phone BLOB
	"""))

class Auth:
    def hash_password(self, password):
        return hashlib.sha256(password.encode()).hexdigest()
    
    def register(self, name, password):
        hash_password = self.hash_password(password)
        try:
            cursor.execute("INSERT INTO users VALUES(?,?)", (name, hash_password))   
            conn.commit()
            print("Registration Successfull")
            
        except:
          print("User Already Exists")  
      def login(self, username, password):
         hash_password = self.hash_password(password)
         cursor.execute("SELECT * FROM users WHERE username=? AND password=?", (username, hash_password))
         return cursor.fetchone()

        class ContactManager():
    def __init__(self, owner):
        self.owner = owner
        
    def encrypt(self, phone):
        return cipher.encrypt(phone.encode())
    
    def decrypt(self, phone):
        return cipher.decrypt(phone).decode()

        
    def add_contact(self, name, phone):
        encrypted_phone = self.encrypt(phone)
        cursor.execute("INSERT INTO contacts (owner,name,phone) VALUES(?, ?, ?)", (self.owner, name, encrypted_phone))
        conn.commit()
        
    def view_contacts(self):
        cursor.execute("SELECT * FROM contacts WHERE owner=?", (self.owner, ))
        records = cursor.fetchall()
        
        if not records:
            print("No Contacts found")
            return
          
        for row in records:  
            decrypte_phone = self.decrypt(row[3])
            print(f"Name = {row[2]} : Contact = {decrypte_phone}")


            def delete_contact(self):
        cursor.execute("DELETE FROM contacts WHERE owner=?", (self.owner, ))
        conn.commit()
        print("Contact Successfully Deleted")
    