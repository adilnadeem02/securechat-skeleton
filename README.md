# Secure Client--Server Encrypted Chat System (IS Assignment 2)

This repository contains a console-based secure chat application built
for **Information Security -- Assignment 2 (Fall 2025)**.\
All cryptographic operations are implemented manually at the
**application layer** (no TLS/SSL).

The system is developed in Python and uses the following cryptographic
components:

-   **AES-128** (ECB mode with PKCS#7 padding)\
-   **RSA-2048** (PKCS#1 v1.5 signatures using SHA-256)\
-   **Diffie--Hellman** for shared key establishment\
-   **SHA-256** hashing\
-   **X.509** certificates signed by a custom CA

The protocol aims to ensure:

✔ Confidentiality\
✔ Integrity\
✔ Authentication\
✔ Non-repudiation

------------------------------------------------------------------------

# 🔧 1. Environment Requirements

The instructions below target **Kali Linux / Debian-based
distributions**.

------------------------------------------------------------------------

## 1.1 Install Required Packages

``` bash
sudo apt update
sudo apt -y upgrade
sudo apt install -y git mariadb-server python3-venv
```

------------------------------------------------------------------------

## 1.2 Secure the MariaDB Installation

``` bash
sudo mariadb-secure-installation
```

Recommended:

-   Set root password → YES\
-   Remove anonymous accounts → YES\
-   Disable remote root login → YES\
-   Delete test database → YES\
-   Apply privilege changes → YES

------------------------------------------------------------------------

# ⚙️ 2. Project Setup

## 2.1 Clone the Repository and Install Dependencies

``` bash
git clone https://github.com/adilnadeem02/securechat-skeleton.git
cd securechat-skeleton
python3 -m venv venv
source venv/bin/activate
pip3 install cryptography mysql-connector-python
```

------------------------------------------------------------------------

## 2.2 Configure Database Credentials

``` sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'YOUR_PASSWORD_HERE';
FLUSH PRIVILEGES;
```

Database schema:

``` sql
CREATE DATABASE secure_chat;
USE secure_chat;

CREATE TABLE users (
    email VARCHAR(255) NOT NULL,
    username VARCHAR(255) NOT NULL UNIQUE,
    salt VARBINARY(16) NOT NULL,
    pwd_hash CHAR(64) NOT NULL,
    PRIMARY KEY (email)
);
```

------------------------------------------------------------------------

## 2.3 Add `config.py`

``` python
DB_CONFIG = {
    'host': 'localhost',
    'user': 'root',
    'password': 'YOUR_PASSWORD_HERE',
    'database': 'secure_chat'
}
```

Ensure it is in `.gitignore`.

------------------------------------------------------------------------

# 🔑 3. Generating Certificates and Keys

``` bash
python3 scripts/gen_ca.py
python3 scripts/gen_cert.py server localhost
python3 scripts/gen_cert.py client client.user
python3 scripts/gen_dh_params.py
```

------------------------------------------------------------------------

# ▶️ 4. Running the Application

## Server

``` bash
python3 server.py
```

## Client

``` bash
python3 client.py
```

------------------------------------------------------------------------

# 💬 5. Features

-   User registration\
-   Login\
-   Encrypted communication\
-   Logout\
-   Signed session receipts

------------------------------------------------------------------------

# 🧪 6. Offline Verification

``` bash
python3 verify_transcript.py client_receipt.json client_transcript.log
```

Expected:

    Hash OK
    Signature VALID
    Transcript authentic and untampered.
