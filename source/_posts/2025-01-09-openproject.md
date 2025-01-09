---
title: openproject
date: 2025-01-09 10:57:57
tags:
- ML
- Trading
- Stock Investment
categories:
- ML
- Finance
---
To set up OpenProject on an **AWS China Ubuntu VM**, follow these steps, including the configuration of a PostgreSQL database:

---

### **1. Prepare Your System**

1. **Update System Packages**:
   ```bash
   sudo apt-get update && sudo apt-get upgrade -y
   ```

2. **Install Essential Dependencies**:
   ```bash
   sudo apt-get install -y wget gnupg2 software-properties-common
   ```

---

### **2. Install and Configure PostgreSQL**

1. **Install PostgreSQL**:
   ```bash
   sudo apt-get install -y postgresql postgresql-contrib
   ```

2. **Start and Enable PostgreSQL Service**:
   ```bash
   sudo systemctl start postgresql
   sudo systemctl enable postgresql
   ```

3. **Configure PostgreSQL to Accept Remote Connections**:
   - **Edit PostgreSQL Configuration**:
     ```bash
     sudo nano /etc/postgresql/12/main/postgresql.conf
     ```
     - Locate the `listen_addresses` parameter and set it to:
       ```plaintext
       listen_addresses = '*'
       ```

   - **Configure Client Authentication**:
     ```bash
     sudo nano /etc/postgresql/12/main/pg_hba.conf
     ```
     - Add the following line to allow connections from any IP with password authentication:
       ```plaintext
       host    all             all             0.0.0.0/0               md5
       ```

   - **Restart PostgreSQL to Apply Changes**:
     ```bash
     sudo systemctl restart postgresql
     ```

4. **Create a Database and User for OpenProject**:
   - Switch to the PostgreSQL user:
     ```bash
     sudo -i -u postgres
     ```
   - Access the PostgreSQL prompt:
     ```bash
     psql
     ```
   - Create a new database user (replace `your_password` with a strong password):
     ```sql
     CREATE USER openprojectuser WITH PASSWORD 'your_password';
     ```
   - Create a new database:
     ```sql
     CREATE DATABASE openprojectdb OWNER openprojectuser;
     ```
   - Grant all privileges on the database to the user:
     ```sql
     GRANT ALL PRIVILEGES ON DATABASE openprojectdb TO openprojectuser;
     ```
   - Exit the PostgreSQL prompt:
     ```sql
     \q
     ```
   - Return to your regular user:
     ```bash
     exit
     ```

---

### **3. Install OpenProject**

1. **Add OpenProject's GPG Key**:
   ```bash
   wget -qO- https://dl.packager.io/srv/opf/openproject/key | sudo apt-key add -
   ```

2. **Add the OpenProject Repository**:
   ```bash
   sudo wget -O /etc/apt/sources.list.d/openproject.list \
     https://dl.packager.io/srv/opf/openproject/stable/12/installer/ubuntu/20.04.repo
   ```

3. **Update Package Lists and Install OpenProject**:
   ```bash
   sudo apt-get update
   sudo apt-get install -y openproject
   ```

---

### **4. Configure OpenProject**

1. **Run the Configuration Wizard**:
   ```bash
   sudo openproject configure
   ```

2. **Database Configuration**:
   - When prompted, select **"Use an existing PostgreSQL database"**.
   - Enter the following details:
     - **Host**: `127.0.0.1`
     - **Port**: `5432`
     - **Database Name**: `openprojectdb`
     - **User**: `openprojectuser`
     - **Password**: The password you set earlier

3. **Web Server Configuration**:
   - Choose to install and configure Apache2 when prompted.

4. **Domain Name Configuration**:
   - Enter the fully qualified domain name (FQDN) or public IP address of your AWS instance.

5. **SSL Configuration**:
   - Decide whether to set up SSL for secure connections. If you have SSL certificates, you can configure them; otherwise, you can skip this step.

6. **Repository and Email Settings**:
   - Configure additional options like Git/SVN repositories and email notifications as per your requirements.

---

### **5. Finalize Installation**

1. **Restart Apache to Apply Changes**:
   ```bash
   sudo systemctl restart apache2
   ```

2. **Access OpenProject**:
   - Open a web browser and navigate to `http://<your_domain_or_ip>`.
   - Log in with the default credentials:
     - **Username**: `admin`
     - **Password**: `admin`
   - For security, change the default password immediately after logging in.

---

**Note**: Ensure that the necessary ports (e.g., 80 for HTTP and 443 for HTTPS) are open in your AWS security groups to allow web traffic.

By following these steps, you will have OpenProject installed and configured with a PostgreSQL database on your AWS China Ubuntu VM. 