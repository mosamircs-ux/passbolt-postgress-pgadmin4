# Passbolt CE & pgAdmin Docker Stack

This repository contains a Docker Compose configuration to easily spin up a self-hosted **Passbolt Community Edition (CE)** instance, backed by a **PostgreSQL** database, and managed with **pgAdmin 4**.

---

## 🚀 Quick Start Guide

Follow these steps to run the stack on a new device.

### 1. Prerequisites
Ensure you have the following installed on the target device:
* **Docker** (v20.10.0 or later)
* **Docker Compose** (v2.0.0 or later)

---

### 2. Launch the Stack
Clone or copy this directory to the new device, open a terminal in the root folder, and run:

```bash
docker compose up -d
```

This will download the required Docker images, create the network/volumes, and start the services in detached (background) mode.

---

### 3. Verify the Services are Running
Check the status of the containers by running:

```bash
docker compose ps
```

All three containers (`db`, `passbolt`, and `pgadmin`) should show as `Up`.

---

## 👤 Registering the First Admin User

Passbolt CE requires you to create your initial administrator account through the command line of the running container.

Run the following command in your terminal:

```powershell
docker compose exec passbolt su -c "/usr/share/php/passbolt/bin/cake passbolt register_user -u your_email@domain.com -f FirstName -l LastName -r admin" -s /bin/bash www-data
```

> [!IMPORTANT]
> Make sure to replace `your_email@domain.com`, `FirstName`, and `LastName` with your actual details.

This command will output a registration link that looks like this:
```text
http://localhost:5880/setup/install/0a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d/9f8e7d6c-5b4a-3f2e-1d0c-9b8a7f6e5d4c
```

1. **Copy the link** from your terminal.
2. **Paste it into your browser**.
3. **Follow the instructions** to generate your GPG keys, set your passphrase, download the recovery kit, and install the Passbolt browser extension.

---

## ⚙️ Service and Port Overview

| Service | Local URL | Port (Host:Container) | Credentials / Default Config |
| :--- | :--- | :--- | :--- |
| **Passbolt Web UI** | [http://localhost:5880](http://localhost:5880) | `5880:80` & `8444:443` | Set up during user registration |
| **pgAdmin 4** | [http://localhost:8080](http://localhost:8080) | `8080:80` | **Email:** `admin@domain.com`<br>**Password:** `P4ssb0lt_pgadmin` |
| **PostgreSQL Database** | *Internal Only* | `5432` | **DB Name:** `passbolt`<br>**User:** `passbolt`<br>**Password:** `P4ssb0lt_db_password` |

---

## 🗄️ Connecting pgAdmin to PostgreSQL

To view and manage the database using pgAdmin:

1. Navigate to [http://localhost:8080](http://localhost:8080).
2. Log in with the pgAdmin credentials:
   * **Email:** `admin@domain.com`
   * **Password:** `P4ssb0lt_pgadmin`
3. Click **Add New Server** and configure it with the following details:
   * **General Tab**:
     * **Name:** `Passbolt DB` (or any custom name)
   * **Connection Tab**:
     * **Host name/address:** `db` *(This matches the database service name in `docker-compose.yaml`)*
     * **Port:** `5432`
     * **Maintenance database:** `passbolt`
     * **Username:** `passbolt`
     * **Password:** `P4ssb0lt_db_password`
4. Click **Save**.

---

## 🛠️ Management & Operations

### Stop the Services
To stop the services without deleting any stored passwords or data:
```bash
docker compose down
```

### View Live Logs
To watch the running logs for troubleshooting:
```bash
docker compose logs -f
```

### Restart a Specific Service (e.g. Passbolt)
```bash
docker compose restart passbolt
```

### ⚠️ Complete Reset (Data Deletion)
If you want to wipe all data (passwords, configurations, database, and pgAdmin configuration) and start fresh:
```bash
docker compose down -v
```
> [!WARNING]
> This command will permanently delete all Docker volumes associated with this project. Any credentials saved in Passbolt will be lost unless you have exported them.
