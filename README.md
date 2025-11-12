# 🏦 Digital Wallet - Microservices System

Sistem microservices untuk aplikasi dompet digital yang dibangun dengan arsitektur modern menggunakan Flask, multiple services (User, Transaction, Notification, Report), dan MySQL sebagai database.

## 📋 Daftar Isi

- [Arsitektur Sistem](#arsitektur-sistem)
- [Teknologi yang Digunakan](#teknologi-yang-digunakan)
- [Prerequisites](#prerequisites)
- [Instalasi dan Setup](#instalasi-dan-setup)
- [Struktur Project](#struktur-project)
- [Service Endpoints](#service-endpoints)
- [Testing dengan Postman](#testing-dengan-postman)
- [Environment Variables](#environment-variables)
- [Integrasi Antarservice](#integrasi-antarservice)
- [Troubleshooting](#troubleshooting)

---

## 🏗️ Arsitektur Sistem

Sistem ini menggunakan arsitektur microservices dengan komponen utama:

```
┌──────────────────┐
│  Client/Browser  │
└────────┬─────────┘
         │
         ▼
┌──────────────────────────────────────┐
│      API Gateway / Load Balancer     │
│      (Optional - untuk production)   │
└────────┬────────────────────────────┘
         │
    ┌────┼────┬────────┬──────────┐
    │    │    │        │          │
    ▼    ▼    ▼        ▼          ▼
┌─────────┐ ┌──────────┐ ┌───────────┐ ┌────────────┐
│  User   │ │Transaction│ │Notification│ │  Report   │
│ Service │ │ Service  │ │ Service   │ │  Service  │
│ :5001   │ │  :5002   │ │  :5003    │ │   :5004   │
└────┬────┘ └─────┬────┘ └─────┬─────┘ └─────┬─────┘
     │           │             │             │
     └───────────┼─────────────┼─────────────┘
                 │             │
                 ▼             ▼
            ┌──────────────────────────┐
            │   MySQL Database         │
            │   (digital_wallet)       │
            │   (digital_wallet_tx)    │
            │   (digital_wallet_notif.)│
            │   (digital_wallet_reports)│
            │   (Port: 3306)           │
            └──────────────────────────┘
```

---

## 🛠️ Teknologi yang Digunakan

### Backend Services

- **Framework**: Python Flask
- **Authentication**: JWT Token & Service Token
- **Database**: MySQL 8.0
- **Communication**: RESTful API

### DevOps

- **Containerization**: Docker & Docker Compose
- **Orchestration**: Docker Compose

### Tools

- **API Testing**: Postman
- **Database Management**: MySQL Workbench / phpMyAdmin

---

## ⚙️ Prerequisites

Pastikan sistem Anda sudah terinstall:

- **Docker** (v20.10+)
- **Docker Compose** (v2.0+)
- **Postman** (untuk testing API)
- **Git** (untuk clone repository)

### System Requirements

- RAM minimum 4GB
- Storage minimum 2GB free space

---

## 🚀 Instalasi dan Setup

### 1. Clone Repository

```bash
git clone <repository-url>
cd digital-wallet-microservices
```

### 2. Setup Environment Variables

Buat file `.env` untuk setiap service:

**user-service/.env**

```env
# Database Configuration
DB_HOST=mysql-db
DB_USER=root
DB_PASSWORD=root
DB_NAME=digital_wallet
DB_PORT=3306

# Service Configuration
PORT=5001
FLASK_ENV=development

# JWT Configuration
JWT_SECRET=your_jwt_secret_key_change_this
JWT_EXPIRATION=3600

# Service Token
SERVICE_TOKEN=service_shared_secret_change_this

# SMTP Configuration (untuk integrasi notifikasi)
SMTP_SERVER=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASSWORD=your_app_password
```

**transaction-service/.env**

```env
# Database Configuration
DB_HOST=mysql-db
DB_USER=root
DB_PASSWORD=root
DB_NAME=digital_wallet_tx
DB_PORT=3306

# Service Configuration
PORT=5002
FLASK_ENV=development

# Service Token
SERVICE_TOKEN=service_shared_secret_change_this

# Service URLs
USER_SERVICE_URL=http://user-service:5001
NOTIFICATION_SERVICE_URL=http://notification-service:5003
```

**notification-service/.env**

```env
# Database Configuration
DB_HOST=mysql-db
DB_USER=root
DB_PASSWORD=root
DB_NAME=digital_wallet_notifications
DB_PORT=3306

# Service Configuration
PORT=5003
FLASK_ENV=development

# Service Token
SERVICE_TOKEN=service_shared_secret_change_this

# Service URLs
USER_SERVICE_URL=http://user-service:5001

# SMTP Configuration
SMTP_SERVER=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASSWORD=your_app_password
SENDER_EMAIL=noreply@digitalwallet.com
```

**report-service/.env**

```env
# Database Configuration
DB_HOST=mysql-db
DB_USER=root
DB_PASSWORD=root
DB_NAME=digital_wallet_reports
DB_PORT=3306

# Service Configuration
PORT=5004
FLASK_ENV=development

# Service Token
SERVICE_TOKEN=service_shared_secret_change_this

# Service URLs
USER_SERVICE_URL=http://user-service:5001
TRANSACTION_SERVICE_URL=http://transaction-service:5002
```

### 3. Build dan Jalankan Containers

```bash
# Build semua images (langkah pertama)
docker-compose build

# Jalankan semua services
docker-compose up -d

# Atau build dan jalankan sekaligus
docker-compose up -d --build
```

### 4. Verifikasi Services

```bash
# Cek status semua containers
docker-compose ps

# Lihat logs semua services
docker-compose logs -f

# Lihat logs service tertentu
docker-compose logs -f user-service
docker-compose logs -f transaction-service
docker-compose logs -f notification-service
docker-compose logs -f report-service

# Test koneksi ke MySQL
docker-compose exec mysql-db mysql -u root -proot -e "SHOW DATABASES;"
```

### 5. Initialize Database (Jika Diperlukan)

```bash
# Login ke MySQL
docker-compose exec mysql-db mysql -u root -proot

# Atau jalankan init script
docker-compose exec mysql-db mysql -u root -proot < init-db.sql
```

---

## 📁 Struktur Project

```
digital-wallet-microservices/
│
├── user-service/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── .env
│   ├── .dockerignore
│   └── app/
│       ├── __init__.py
│       ├── main.py
│       ├── models.py
│       ├── routes.py
│       ├── auth.py
│       └── database.py
│
├── transaction-service/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── .env
│   ├── .dockerignore
│   └── app/
│       ├── __init__.py
│       ├── main.py
│       ├── models.py
│       ├── routes.py
│       └── database.py
│
├── notification-service/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── .env
│   ├── .dockerignore
│   └── app/
│       ├── __init__.py
│       ├── main.py
│       ├── models.py
│       ├── routes.py
│       ├── email_service.py
│       └── database.py
│
├── report-service/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── .env
│   ├── .dockerignore
│   └── app/
│       ├── __init__.py
│       ├── main.py
│       ├── models.py
│       ├── routes.py
│       └── database.py
│
├── docker-compose.yml
├── init-db.sql
├── digital-wallet-collection.json (Postman)
└── README.md
```

---

## 🔌 Service Endpoints

Semua endpoints diakses melalui base URL yang sesuai dengan service masing-masing.

### 1️⃣ User Service (Port 5001)

**Base URL**: `http://localhost:5001`

#### Authentication Routes

**POST /auth/register** - Register User

```http
POST http://localhost:5001/auth/register
Content-Type: application/json

{
    "full_name": "John Doe",
    "email": "john@example.com",
    "password": "password123"
}

Response (201 Created):
{
    "id": 1,
    "full_name": "John Doe",
    "email": "john@example.com",
    "balance": 0.0
}
```

**POST /auth/login** - Login User

```http
POST http://localhost:5001/auth/login
Content-Type: application/json

{
    "email": "john@example.com",
    "password": "password123"
}

Response (200 OK):
{
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
        "id": 1,
        "full_name": "John Doe",
        "email": "john@example.com"
    }
}
```

#### User CRUD Routes

**GET /users** - Get All Users

```http
GET http://localhost:5001/users

Response (200 OK):
[
    {
        "id": 1,
        "full_name": "John Doe",
        "email": "john@example.com",
        "balance": 10000.0
    },
    {
        "id": 2,
        "full_name": "Jane Doe",
        "email": "jane@example.com",
        "balance": 25000.0
    }
]
```

**POST /users** - Create User

```http
POST http://localhost:5001/users
Content-Type: application/json

{
    "full_name": "Jane Doe",
    "email": "jane@example.com",
    "password": "mypassword"
}

Response (201 Created):
{
    "id": 2,
    "full_name": "Jane Doe",
    "email": "jane@example.com"
}
```

**GET /users/{id}** - Get User by ID

```http
GET http://localhost:5001/users/1
Authorization: Bearer {token} (optional)

Response (200 OK):
{
    "id": 1,
    "full_name": "John Doe",
    "email": "john@example.com",
    "balance": 50000.0
}
```

**PUT /users/{id}** - Update User

```http
PUT http://localhost:5001/users/1
Authorization: Bearer {token}
Content-Type: application/json

{
    "full_name": "John D. Doe"
}

Response (200 OK):
{
    "id": 1,
    "full_name": "John D. Doe",
    "email": "john@example.com"
}
```

**DELETE /users/{id}** - Delete User

```http
DELETE http://localhost:5001/users/1
Authorization: Bearer {token}

Response (200 OK):
{
    "message": "User deleted successfully"
}
```

#### Balance Routes

**GET /users/{id}/balance** - Get Balance

```http
GET http://localhost:5001/users/1/balance

Response (200 OK):
{
    "user_id": 1,
    "balance": 50000.0
}
```

**POST /users/{id}/topup** - Top Up Balance

```http
POST http://localhost:5001/users/1/topup
Authorization: Bearer {token}
Content-Type: application/json

{
    "amount": 100000
}

Response (200 OK):
{
    "id": 1,
    "balance": 150000.0
}
```

**POST /users/{id}/debit** - Debit Balance

```http
POST http://localhost:5001/users/1/debit
Authorization: Bearer {token}
Content-Type: application/json

{
    "amount": 50000
}

Response (200 OK):
{
    "id": 1,
    "balance": 100000.0
}
```

#### Internal Routes (Service-to-Service)

**GET /internal/users/{id}** - Get User (Internal)

```http
GET http://localhost:5001/internal/users/1
X-Service-Token: service_shared_secret_change_this

Response (200 OK):
{
    "id": 1,
    "full_name": "John Doe",
    "email": "john@example.com"
}
```

**PUT /internal/users/{id}/balance** - Update Balance (Internal)

```http
PUT http://localhost:5001/internal/users/1/balance
X-Service-Token: service_shared_secret_change_this
Content-Type: application/json

{
    "action": "debit",
    "amount": 5000
}

Response (200 OK):
{
    "id": 1,
    "balance": 45000.0
}
```

---

### 2️⃣ Transaction Service (Port 5002)

**Base URL**: `http://localhost:5002`

#### Transaction Management Routes

**POST /transactions** - Create Transaction

```http
POST http://localhost:5002/transactions
X-Service-Token: service_shared_secret_change_this
Content-Type: application/json

{
    "sender_id": 1,
    "receiver_id": 2,
    "amount": 10000,
    "description": "Payment for goods"
}

Response (201 Created):
{
    "id": 10,
    "sender_id": 1,
    "receiver_id": 2,
    "amount": 10000,
    "status": "pending",
    "description": "Payment for goods",
    "created_at": "2025-11-12T10:30:00Z"
}
```

**GET /transactions** - Get All Transactions

```http
GET http://localhost:5002/transactions

Response (200 OK):
[
    {
        "id": 1,
        "sender_id": 1,
        "receiver_id": 2,
        "amount": 50000,
        "status": "completed",
        "description": "Topup",
        "created_at": "2025-11-10T10:00:00Z"
    },
    {
        "id": 2,
        "sender_id": 2,
        "receiver_id": 3,
        "amount": 20000,
        "status": "pending",
        "description": "Transfer",
        "created_at": "2025-11-11T08:15:00Z"
    }
]
```

**GET /transactions/{id}** - Get Transaction by ID

```http
GET http://localhost:5002/transactions/1

Response (200 OK):
{
    "id": 1,
    "sender_id": 1,
    "receiver_id": 2,
    "amount": 50000,
    "status": "completed",
    "description": "Payment completed",
    "created_at": "2025-11-10T10:00:00Z"
}
```

**PUT /transactions/{id}** - Update Transaction Status

```http
PUT http://localhost:5002/transactions/1
Content-Type: application/json

{
    "status": "completed"
}

Response (200 OK):
{
    "id": 1,
    "sender_id": 1,
    "receiver_id": 2,
    "amount": 50000,
    "status": "completed",
    "description": "Payment completed",
    "updated_at": "2025-11-12T14:00:00Z"
}
```

**DELETE /transactions/{id}** - Delete Transaction

```http
DELETE http://localhost:5002/transactions/1

Response (200 OK):
{
    "message": "Transaction deleted successfully"
}
```

**GET /transactions/user/{user_id}** - Get Transactions by User

```http
GET http://localhost:5002/transactions/user/1

Response (200 OK):
[
    {
        "id": 1,
        "sender_id": 1,
        "receiver_id": 2,
        "amount": 10000,
        "status": "completed",
        "description": "Purchase item",
        "created_at": "2025-11-10T09:00:00Z"
    },
    {
        "id": 2,
        "sender_id": 1,
        "receiver_id": 3,
        "amount": 5000,
        "status": "pending",
        "description": "Transfer",
        "created_at": "2025-11-11T08:00:00Z"
    }
]
```

---

### 3️⃣ Notification Service (Port 5003)

**Base URL**: `http://localhost:5003`

#### Notification Management Routes

**POST /notifications** - Create Notification

```http
POST http://localhost:5003/notifications
X-Service-Token: service_shared_secret_change_this
Content-Type: application/json

{
    "user_id": 1,
    "message": "Your payment of Rp 50,000 has been successfully processed."
}

Response (201 Created):
{
    "id": 1,
    "user_id": 1,
    "message": "Your payment of Rp 50,000 has been successfully processed.",
    "status": "pending",
    "created_at": "2025-11-12T10:30:00Z"
}
```

**GET /notifications** - Get All Notifications

```http
GET http://localhost:5003/notifications

Response (200 OK):
[
    {
        "id": 1,
        "user_id": 1,
        "message": "Top-up Rp 100,000 successful.",
        "status": "sent",
        "created_at": "2025-11-10T09:00:00Z"
    },
    {
        "id": 2,
        "user_id": 2,
        "message": "Transaction pending approval.",
        "status": "pending",
        "created_at": "2025-11-11T08:00:00Z"
    }
]
```

**GET /notifications/{id}** - Get Notification by ID

```http
GET http://localhost:5003/notifications/1

Response (200 OK):
{
    "id": 1,
    "user_id": 1,
    "message": "Top-up Rp 100,000 successful.",
    "status": "sent",
    "created_at": "2025-11-10T09:00:00Z"
}
```

**PUT /notifications/{id}** - Update Notification

```http
PUT http://localhost:5003/notifications/1
Content-Type: application/json

{
    "message": "Your payment is being processed.",
    "status": "pending"
}

Response (200 OK):
{
    "id": 1,
    "user_id": 1,
    "message": "Your payment is being processed.",
    "status": "pending",
    "updated_at": "2025-11-12T10:45:00Z"
}
```

**DELETE /notifications/{id}** - Delete Notification

```http
DELETE http://localhost:5003/notifications/1

Response (200 OK):
{
    "message": "Notification deleted successfully"
}
```

**POST /notifications/{id}/send** - Send Notification via Email

```http
POST http://localhost:5003/notifications/1/send

Response (200 OK):
{
    "message": "Notification sent successfully",
    "status": "sent"
}
```

**POST /notifications/{id}/resend** - Resend Failed Notification

```http
POST http://localhost:5003/notifications/1/resend

Response (200 OK):
{
    "message": "Notification resent successfully",
    "status": "sent"
}
```

---

### 4️⃣ Report Service (Port 5004)

**Base URL**: `http://localhost:5004`

#### Report Management Routes

**POST /reports** - Create Report

```http
POST http://localhost:5004/reports
X-Service-Token: service_shared_secret_change_this
Content-Type: application/json

{
    "user_id": 1,
    "transaction_id": 10
}

Response (201 Created):
{
    "id": 5,
    "user_id": 1,
    "transaction_id": 10,
    "status": "generated",
    "created_at": "2025-11-12T10:30:00Z"
}
```

**GET /reports/user/{user_id}** - Get Reports by User (with Pagination)

```http
GET http://localhost:5004/reports/user/1?page=1&per_page=50

Response (200 OK):
{
    "user_id": 1,
    "page": 1,
    "per_page": 50,
    "total": 2,
    "reports": [
        {
            "id": 1,
            "transaction_id": 10,
            "amount": 50000,
            "status": "completed",
            "created_at": "2025-11-10T09:00:00Z"
        },
        {
            "id": 2,
            "transaction_id": 11,
            "amount": 10000,
            "status": "pending",
            "created_at": "2025-11-11T08:15:00Z"
        }
    ]
}
```

**GET /summaries/user/{user_id}** - Get Summary by User

```http
GET http://localhost:5004/summaries/user/1

Response (200 OK):
{
    "user_id": 1,
    "total_transactions": 25,
    "total_spent": 2500000,
    "total_received": 1800000,
    "last_transaction": "2025-11-12T08:00:00Z"
}
```

**POST /sync/all** - Sync All Reports

```http
POST http://localhost:5004/sync/all
X-Service-Token: service_shared_secret_change_this

Response (200 OK):
{
    "message": "Reports synchronized successfully",
    "synced_users": 125,
    "synced_transactions": 1450,
    "timestamp": "2025-11-12T10:45:00Z"
}
```

---

## 🧪 Testing dengan Postman

### Import Collection

1. **Buka Postman**
2. **Klik Import** di top-left
3. **Pilih file** `digital-wallet-collection.json`
4. **Collection akan ter-import** dengan semua endpoints

### Testing Flow Recommended

Urutan testing yang disarankan untuk memahami alur sistem:

#### 1. User Service Testing

```
1. POST /auth/register - Register user baru
2. POST /auth/login - Login untuk mendapatkan JWT token
3. GET /users - Lihat semua users
4. GET /users/{id} - Lihat detail user tertentu
5. PUT /users/{id} - Update data user
6. GET /users/{id}/balance - Cek saldo user
7. POST /users/{id}/topup - Top up saldo
8. POST /users/{id}/debit - Debit saldo
9. DELETE /users/{id} - Hapus user (optional)
```

#### 2. Transaction Service Testing

```
1. POST /transactions - Buat transaksi baru
2. GET /transactions - Lihat semua transaksi
3. GET /transactions/{id} - Lihat detail transaksi
4. GET /transactions/user/{user_id} - Lihat transaksi per user
5. PUT /transactions/{id} - Update status transaksi
6. DELETE /transactions/{id} - Hapus transaksi
```

#### 3. Notification Service Testing

```
1. POST /notifications - Buat notifikasi
2. GET /notifications - Lihat semua notifikasi
3. GET /notifications/{id} - Lihat detail notifikasi
4. POST /notifications/{id}/send - Kirim notifikasi via email
5. POST /notifications/{id}/resend - Kirim ulang notifikasi
6. PUT /notifications/{id} - Update notifikasi
7. DELETE /notifications/{id} - Hapus notifikasi
```

#### 4. Report Service Testing

```
1. POST /reports - Buat report
2. GET /reports/user/{user_id} - Lihat laporan per user
3. GET /summaries/user/{user_id} - Lihat ringkasan user
4. POST /sync/all - Sinkronisasi semua laporan
```

### Collection Variables

Postman collection sudah dilengkapi dengan variables untuk kemudahan:

| Variable                | Default                           | Auto-updated     |
| ----------------------- | --------------------------------- | ---------------- |
| `base_url_user`         | http://localhost:5001             | -                |
| `base_url_transaction`  | http://localhost:5002             | -                |
| `base_url_notification` | http://localhost:5003             | -                |
| `base_url_report`       | http://localhost:5004             | -                |
| `jwt_token`             | (empty)                           | ✅ Setelah login |
| `user_id`               | (empty)                           | ✅ Setelah login |
| `transaction_id`        | 1                                 | -                |
| `service_token`         | service_shared_secret_change_this | -                |

### Cara Menggunakan Collection Variables

1. **Login dulu** untuk mendapatkan JWT token
2. **Token otomatis tersimpan** di variable `jwt_token`
3. **Semua request berikutnya** akan menggunakan token ini
4. **User ID juga tersimpan** untuk request selanjutnya

---

## 🔧 Environment Variables

### User Service Configuration

| Variable         | Description              | Example                             |
| ---------------- | ------------------------ | ----------------------------------- |
| `DB_HOST`        | MySQL host               | `mysql-db`                          |
| `DB_USER`        | Database user            | `root`                              |
| `DB_PASSWORD`    | Database password        | `root`                              |
| `DB_NAME`        | Database name            | `digital_wallet`                    |
| `DB_PORT`        | Database port            | `3306`                              |
| `PORT`           | Service port             | `5001`                              |
| `FLASK_ENV`      | Flask environment        | `development`                       |
| `JWT_SECRET`     | JWT secret key           | `your_jwt_secret_key_change_this`   |
| `JWT_EXPIRATION` | JWT expiration (seconds) | `3600`                              |
| `SERVICE_TOKEN`  | Service-to-service token | `service_shared_secret_change_this` |

### Transaction Service Configuration

| Variable                   | Description              | Example                             |
| -------------------------- | ------------------------ | ----------------------------------- |
| `DB_HOST`                  | MySQL host               | `mysql-db`                          |
| `DB_USER`                  | Database user            | `root`                              |
| `DB_PASSWORD`              | Database password        | `root`                              |
| `DB_NAME`                  | Database name            | `digital_wallet_tx`                 |
| `DB_PORT`                  | Database port            | `3306`                              |
| `PORT`                     | Service port             | `5002`                              |
| `FLASK_ENV`                | Flask environment        | `development`                       |
| `SERVICE_TOKEN`            | Service-to-service token | `service_shared_secret_change_this` |
| `USER_SERVICE_URL`         | User service URL         | `http://user-service:5001`          |
| `NOTIFICATION_SERVICE_URL` | Notification service URL | `http://notification-service:5003`  |

### Notification Service Configuration

| Variable           | Description              | Example                             |
| ------------------ | ------------------------ | ----------------------------------- |
| `DB_HOST`          | MySQL host               | `mysql-db`                          |
| `DB_USER`          | Database user            | `root`                              |
| `DB_PASSWORD`      | Database password        | `root`                              |
| `DB_NAME`          | Database name            | `digital_wallet_notifications`      |
| `DB_PORT`          | Database port            | `3306`                              |
| `PORT`             | Service port             | `5003`                              |
| `FLASK_ENV`        | Flask environment        | `development`                       |
| `SERVICE_TOKEN`    | Service-to-service token | `service_shared_secret_change_this` |
| `USER_SERVICE_URL` | User service URL         | `http://user-service:5001`          |
| `SMTP_SERVER`      | SMTP server              | `smtp.gmail.com`                    |
| `SMTP_PORT`        | SMTP port                | `587`                               |
| `SMTP_USER`        | SMTP username            | `your_email@gmail.com`              |
| `SMTP_PASSWORD`    | SMTP password            | `your_app_password`                 |
| `SENDER_EMAIL`     | Sender email             | `noreply@digitalwallet.com`         |

### Report Service Configuration

| Variable                  | Description              | Example                             |
| ------------------------- | ------------------------ | ----------------------------------- |
| `DB_HOST`                 | MySQL host               | `mysql-db`                          |
| `DB_USER`                 | Database user            | `root`                              |
| `DB_PASSWORD`             | Database password        | `root`                              |
| `DB_NAME`                 | Database name            | `digital_wallet_reports`            |
| `DB_PORT`                 | Database port            | `3306`                              |
| `PORT`                    | Service port             | `5004`                              |
| `FLASK_ENV`               | Flask environment        | `development`                       |
| `SERVICE_TOKEN`           | Service-to-service token | `service_shared_secret_change_this` |
| `USER_SERVICE_URL`        | User service URL         | `http://user-service:5001`          |
| `TRANSACTION_SERVICE_URL` | Transaction service URL  | `http://transaction-service:5002`   |

---

## 🧠 Integrasi Antarservice

### Service Communication Flow

```
┌─────────────────┐
│   User Service  │
│    (5001)       │
└────────┬────────┘
         │ (Internal API)
         │ GET /internal/users/{id}
         │ PUT /internal/users/{id}/balance
         │
    ┌────▼────────────────────────┐
    │                             │
    ▼                             ▼
┌──────────────┐           ┌──────────────┐
│ Transaction  │           │ Notification │
│  Service     │           │   Service    │
│  (5002)      │           │   (5003)     │
└──────┬───────┘           └──────────────┘
       │                          ▲
       │ POST /transactions       │
       │ (creates transactions)   │ POST /notifications
       │                          │ (sends notifications)
       │                          │
       └──────────────┬───────────┘
                      │
                      ▼
            ┌──────────────────┐
            │  Report Service  │
            │   (5004)         │
            └──────────────────┘
```

### Integration Details

#### User Service ↔ Transaction Service

**Saat transaksi dibuat, Transaction Service memanggil User Service:**

```bash
PUT http://user-service:5001/internal/users/{user_id}/balance
X-Service-Token: service_shared_secret_change_this
Content-Type: application/json

{
    "action": "debit",
    "amount": 50000
}
```

#### Transaction Service ↔ Notification Service

**Saat transaksi selesai, Transaction Service memicu Notification Service:**

```bash
POST http://notification-service:5003/notifications
X-Service-Token: service_shared_secret_change_this
Content-Type: application/json

{
    "user_id": 1,
    "message": "Your transaction has been completed successfully"
}
```

#### Report Service ↔ User & Transaction Service

**Report Service mengambil data dari User dan Transaction Service untuk membuat laporan:**

```bash
# Ambil data user
GET http://user-service:5001/internal/users/{user_id}
X-Service-Token: service_shared_secret_change_this

# Ambil data transaksi
GET http://transaction-service:5002/transactions/user/{user_id}
```

### Service Token Security

Service token digunakan untuk memverifikasi bahwa request berasal dari service tertentu:

```python
# Contoh di Flask
@app.before_request
def verify_service_token():
    if request.path.startswith('/internal/'):
        token = request.headers.get('X-Service-Token')
        if token != os.getenv('SERVICE_TOKEN'):
            return {'error': 'Unauthorized'}, 401
```

---

## 🐛 Troubleshooting

### 1. Services Tidak Bisa Connect ke MySQL

**Problem**: Services error saat startup, tidak bisa konek ke MySQL

```bash
# Solusi: Cek health status MySQL
docker-compose ps

# Tunggu hingga MySQL healthy (status "healthy")
docker-compose logs mysql-db

# Jika masih error, restart services
docker-compose restart user-service transaction-service notification-service report-service

# Tunggu beberapa detik setelah MySQL ready
```

### 2. Port Sudah Digunakan

**Problem**: Error "Address already in use" saat menjalankan `docker-compose up`

```bash
# Solusi 1: Cek port yang terpakai
lsof -i :5001  # User Service
lsof -i :5002  # Transaction Service
lsof -i :5003  # Notification Service
lsof -i :5004  # Report Service
lsof -i :3306  # MySQL

# Solusi 2: Edit docker-compose.yml untuk menggunakan port berbeda
# Ubah "5001:5001" menjadi "5011:5001" (gunakan port 5011 di host)

# Solusi 3: Matikan aplikasi yang menggunakan port tersebut
sudo kill -9 <PID>
```

### 3. JWT Token Expired

**Problem**: Response error "Token has expired" saat call protected endpoint

```bash
# Solusi: Login ulang untuk mendapatkan token baru
POST http://localhost:5001/auth/login
{
    "email": "john@example.com",
    "password": "password123"
}

# Copy token baru ke Authorization header
Authorization: Bearer {new_token}
```

### 4. Database Connection Error

**Problem**: Error "Can't connect to MySQL server"

```bash
# Solusi 1: Cek koneksi database
docker-compose exec mysql-db mysql -u root -proot -e "SELECT 1;"

# Solusi 2: Lihat logs MySQL
docker-compose logs mysql-db

# Solusi 3: Verifikasi environment variables
docker-compose exec user-service env | grep DB_

# Solusi 4: Restart MySQL
docker-compose restart mysql-db
```

### 5. Service Crashing / Not Starting

**Problem**: Container service tidak bisa start, exit dengan error

```bash
# Solusi 1: Lihat logs detail
docker-compose logs -f user-service --tail=100

# Solusi 2: Cek dependency services
docker-compose ps

# Solusi 3: Rebuild service
docker-compose build user-service

# Solusi 4: Restart dengan verbose output
docker-compose up user-service

# Solusi 5: Check Python errors
docker-compose exec user-service python app/main.py
```

### 6. SMTP/Email Error

**Problem**: Notifikasi tidak terkirim, error "SMTP connection failed"

```bash
# Solusi 1: Verifikasi SMTP credentials di .env
docker-compose exec notification-service env | grep SMTP_

# Solusi 2: Jika menggunakan Gmail, gunakan App Password bukan password utama
# 1. Aktifkan 2-factor authentication di Gmail
# 2. Generate App Password di https://myaccount.google.com/apppasswords
# 3. Update SMTP_PASSWORD di .env dengan App Password

# Solusi 3: Cek port SMTP
docker-compose exec notification-service curl -v smtp.gmail.com:587

# Solusi 4: Test email connection
docker-compose exec notification-service python -c "
import smtplib
server = smtplib.SMTP('smtp.gmail.com', 587)
server.starttls()
server.login('your_email@gmail.com', 'your_app_password')
print('SMTP connection successful')
"
```

### 7. Reset Database

**Problem**: Ingin mereset semua data dan mulai dari awal

```bash
# HATI-HATI: Ini akan menghapus SEMUA data!

# Solusi 1: Stop services
docker-compose down

# Solusi 2: Hapus volumes (data akan terhapus)
docker-compose down -v

# Solusi 3: Rebuild dan start ulang
docker-compose up -d --build

# Solusi 4: Re-initialize database jika ada script
docker-compose exec mysql-db mysql -u root -proot < init-db.sql
```

### 8. Rebuild Service Tertentu

**Problem**: Ingin rebuild hanya satu service saja

```bash
# Rebuild single service
docker-compose build user-service

# Restart service tersebut
docker-compose up -d user-service

# Atau sekaligus dalam satu command
docker-compose up -d --build user-service
```

### 9. Cek Logs Error

**Problem**: Perlu melihat log error secara detail

```bash
# Logs semua services (last 50 lines)
docker-compose logs --tail=50

# Logs service spesifik (follow mode, real-time)
docker-compose logs -f user-service
docker-compose logs -f transaction-service
docker-compose logs -f notification-service
docker-compose logs -f report-service

# Logs MySQL
docker-compose logs -f mysql-db

# Logs dengan timestamp
docker-compose logs --timestamps
```

### 10. Container Tidak Bisa Start

**Problem**: Container restart terus menerus atau langsung exit

```bash
# Solusi 1: Cek status detail
docker-compose ps

# Solusi 2: Lihat exit code dan error
docker-compose logs [service-name]

# Solusi 3: Rebuild dari scratch
docker-compose down
docker system prune -a
docker-compose build --no-cache
docker-compose up -d

# Solusi 4: Debug manual
docker-compose run --rm user-service bash
# Di dalam container:
python app/main.py
```

### 11. Network Error Antar Services

**Problem**: Services tidak bisa communicate antar container

```bash
# Solusi 1: Verifikasi service URLs di .env
# USER_SERVICE_URL harus: http://user-service:5001 (bukan localhost)

# Solusi 2: Test koneksi antar container
docker-compose exec transaction-service curl http://user-service:5001/users

# Solusi 3: Cek network
docker network ls
docker network inspect [network-name]

# Solusi 4: Restart services
docker-compose restart
```

### 12. Postman Collection Error

**Problem**: Import collection Postman gagal atau request tidak bekerja

```bash
# Solusi 1: Update base URLs di Postman
# Pastikan semua base_url sesuai dengan port services

# Solusi 2: Clear collection cache
# Logout → delete cookies → logout

# Solusi 3: Re-import collection
# File → Import → Pilih file JSON lagi

# Solusi 4: Manual set authorization header
Authorization: Bearer {jwt_token_dari_login}
```

---

## 📝 Catatan Penting

### JWT Token

- **Expiration**: Token secara default berlaku 1 jam (3600 detik)
- **Jika expired**: Lakukan login ulang untuk mendapatkan token baru
- **Aman**: Jangan share token dengan orang lain

### Database

- **Auto-initialization**: Database dan tables dibuat otomatis melalui `init-db.sql`
- **Persistence**: Data tersimpan dalam Docker volume (tidak hilang saat container restart)
- **Backup**: Untuk backup data, gunakan `docker-compose exec mysql-db mysqldump -u root -proot digital_wallet > backup.sql`

### Service Token

- **Purpose**: Untuk verifikasi komunikasi antar service
- **Default**: `service_shared_secret_change_this`
- **Production**: HARUS diubah dengan token yang aman dan kompleks

### Health Check

- **MySQL**: Memiliki health check 30 detik, service lain akan wait hingga MySQL ready
- **Services**: Tunggu 2-3 detik setelah MySQL ready sebelum test

### Port Mapping

| Service              | Internal | External | URL                   |
| -------------------- | -------- | -------- | --------------------- |
| User Service         | 5001     | 5001     | http://localhost:5001 |
| Transaction Service  | 5002     | 5002     | http://localhost:5002 |
| Notification Service | 5003     | 5003     | http://localhost:5003 |
| Report Service       | 5004     | 5004     | http://localhost:5004 |
| MySQL                | 3306     | 3306     | localhost:3306        |

---

## 🛑 Shutdown / Stop Services

### Graceful Shutdown

```bash
# Stop semua services (data tetap tersimpan)
docker-compose down

# Stop services dengan preserve volumes
docker-compose stop

# Stop service tertentu saja
docker-compose stop user-service
```

### Force Stop & Remove Everything

```bash
# Stop dan remove semua containers, networks
docker-compose down

# Stop dan remove INCLUDING volumes (HATI-HATI: data akan terhapus)
docker-compose down -v

# Remove unused images
docker image prune -a
```

---

## 🔐 Security Best Practices

1. **Change Default Credentials**

   ```env
   DB_PASSWORD=very_strong_password_here
   JWT_SECRET=very_secure_jwt_secret_key_here
   SERVICE_TOKEN=very_secure_service_token_here
   ```

2. **Use Environment Variables**

   - Jangan hardcode secret keys di source code
   - Gunakan `.env` file dan jangan commit ke git

3. **HTTPS in Production**

   - Gunakan reverse proxy (nginx/Apache) dengan SSL
   - Implement CORS properly

4. **Database Security**

   - Ganti default MySQL password
   - Limit database user privileges
   - Use strong passwords

5. **API Rate Limiting**
   - Implement rate limiting untuk prevent abuse
   - Use authentication untuk sensitive endpoints

---

## 📞 Support & Debugging

Jika mengalami kendala:

1. **Check Logs**

   ```bash
   docker-compose logs -f [service-name]
   ```

2. **Verify Configuration**

   ```bash
   docker-compose config
   ```

3. **Test Connectivity**

   ```bash
   docker-compose exec [service] curl http://[other-service]:port/health
   ```

4. **Common Issues**: Lihat bagian [Troubleshooting](#troubleshooting) di atas

5. **Documentation**: Refer ke README masing-masing service

---

## 📊 API Documentation Summary

### Service Roles

| Service                  | Function                                 | Port |
| ------------------------ | ---------------------------------------- | ---- |
| **User Service**         | Authentication, user management, balance | 5001 |
| **Transaction Service**  | Transaction processing, balance updates  | 5002 |
| **Notification Service** | Email notifications, alerts              | 5003 |
| **Report Service**       | Analytics, reporting, summaries          | 5004 |

### Authentication Methods

| Type              | Usage                  | Header                          |
| ----------------- | ---------------------- | ------------------------------- |
| **JWT Token**     | User API calls         | `Authorization: Bearer {token}` |
| **Service Token** | Internal service calls | `X-Service-Token: {token}`      |

### Response Codes

| Code    | Meaning                        |
| ------- | ------------------------------ |
| **200** | OK - Request successful        |
| **201** | Created - Resource created     |
| **400** | Bad Request - Invalid input    |
| **401** | Unauthorized - Auth required   |
| **403** | Forbidden - Access denied      |
| **404** | Not Found - Resource not found |
| **500** | Server Error - Internal error  |

---

## 🎉 Next Steps

1. ✅ Setup services dengan `docker-compose up -d --build`
2. ✅ Import Postman collection
3. ✅ Test endpoints sesuai urutan yang disarankan
4. ✅ Monitor logs untuk debug jika ada error
5. ✅ Develop features sesuai requirement

---

**Happy Coding! 🚀**

**Last Updated**: November 2025  
**Version**: 1.0.0
