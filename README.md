# Inception 🐳

A 42 School project focused on system administration and containerization using Docker. The goal is to set up a small infrastructure composed of multiple Docker containers (NGINX, WordPress, MariaDB) orchestrated with `docker-compose`.

## 📋 Table of Contents

- [About The Project](#about-the-project)
- [Core Concepts](#core-concepts)
- [Services](#services)
- [Technologies Used](#technologies-used)
- [Installation](#installation)
- [Usage](#usage)
- [Configuration Highlights](#configuration-highlights)
- [Project Structure](#project-structure)
- [Makefile Targets](#makefile-targets)
- [Author](#author)
- [Notes](#notes)

## 🎯 About The Project

Inception requires setting up a multi-container application using Docker. This project aims to deepen understanding of:
- **Docker:** Creating and managing Docker images and containers.
- **Docker Compose:** Orchestrating multi-container applications.
- **Networking:** Configuring communication between containers and with the host.
- **Data Persistence:** Using Docker volumes to persist data (e.g., WordPress files, MariaDB database).
- **System Administration:** Basic setup and configuration of services like NGINX, WordPress, and MariaDB.
- **Security:** Basic security practices, including environment variables for secrets.

The final setup typically involves an NGINX server acting as a reverse proxy for a WordPress site, which in turn uses a MariaDB database for its data.

## 🧠 Core Concepts

- **Containerization:** Isolating applications in lightweight, portable containers.
- **Image Building:** Creating custom Docker images using `Dockerfile`.
- **Orchestration:** Defining and managing the lifecycle of multiple services with `docker-compose.yml`.
- **Service Discovery:** How containers find and communicate with each other within the Docker network.
- **Data Management:** Ensuring data persistence across container restarts using volumes.
- **Environment Variables:** Securely passing configuration and secrets to containers.

## 🛠️ Services

The project typically consists of the following services running in separate Docker containers:

1.  **NGINX:**
    *   Acts as a web server and reverse proxy.
    *   Serves the WordPress application.
    *   Often configured with SSL/TLS for HTTPS.
    *   Built from an official NGINX image or a custom Dockerfile based on a lightweight OS (e.g., Alpine).

2.  **WordPress:**
    *   A popular content management system (CMS).
    *   Requires a PHP environment (often PHP-FPM).
    *   Connects to the MariaDB database to store its content.
    *   Built from an official WordPress image or a custom Dockerfile.
    *   Its files (`wp-content`, etc.) are stored in a Docker volume.

3.  **MariaDB:**
    *   A relational database server, a fork of MySQL.
    *   Stores all WordPress data (posts, users, settings, etc.).
    *   Its database files are stored in a Docker volume.
    *   Built from an official MariaDB image or a custom Dockerfile.

## ✨ Technologies Used

- **Docker**
- **Docker Compose**
- **NGINX**
- **WordPress** (with PHP-FPM)
- **MariaDB**
- **Shell Scripting** (for setup or entrypoint scripts)
- **Makefile** (for build and management commands)
- **Linux Environment** (containers are typically Linux-based)
- (Optionally) Redis for caching, Adminer/phpMyAdmin for database management, etc., though the core project usually focuses on the three main services.

## 🚀 Installation

Prerequisites:
- **Docker Engine**
- **Docker Compose**

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/Marouane0107/Inception.git
    cd Inception
    ```

2.  **Set up environment variables:**
    The project often requires a `.env` file at the root of the project to store sensitive information like database passwords, usernames, etc. Create a `.env` file based on the requirements (e.g., from a provided `example.env` or the subject).
    Example `.env` content:
    ```env
    # Domain Name (used for NGINX server_name and WordPress site URL)
    DOMAIN_NAME=yourlogin.42.fr # Replace yourlogin with your actual 42 login

    # MariaDB Credentials
    MYSQL_DATABASE=wordpress_db
    MYSQL_USER=wp_user
    MYSQL_PASSWORD=secure_password
    MYSQL_ROOT_PASSWORD=very_secure_root_password

    # WordPress Admin Credentials (optional, can be set during WP setup)
    # WP_ADMIN_USER=admin
    # WP_ADMIN_PASSWORD=adminpass
    # WP_ADMIN_EMAIL=admin@example.com
    ```
    *Ensure your `DOMAIN_NAME` is correctly configured in `/etc/hosts` on your host machine to point to `127.0.0.1` for local testing, e.g.:*
    ```
    127.0.0.1 yourlogin.42.fr
    ```

3.  **Build and start the containers:**
    Use the `Makefile` to build the images and run the services.
    ```bash
    make
    ```
    This command typically executes `docker-compose up --build -d`.

## 🎮 Usage

-   **Access WordPress:** Open your web browser and navigate to `https://<your_DOMAIN_NAME>` (e.g., `https://yourlogin.42.fr`). You should be redirected to the WordPress setup page or your site if already configured.
-   **Access Database (if a tool like Adminer is included):** Navigate to the appropriate URL (e.g., `https://<your_DOMAIN_NAME>:8080` if Adminer is configured on port 8080).

### Managing Services:

-   **Start all services:**
    ```bash
    make # or make up
    ```
-   **Stop all services:**
    ```bash
    make down # or docker-compose down
    ```
-   **View logs:**
    ```bash
    make logs # or docker-compose logs -f
    docker-compose logs <service_name> # For a specific service
    ```
-   **Rebuild images and restart:**
    ```bash
    make re
    ```
-   **Clean up (stop containers, remove containers, networks, volumes, images):**
    ```bash
    make clean   # Typically removes containers, networks
    make fclean  # Typically `clean` + removes volumes and images
    ```

## 🔧 Configuration Highlights

-   **`docker-compose.yml`:** The central file defining all services, networks, and volumes.
    -   `services`: Defines NGINX, WordPress, MariaDB.
    -   `build`: Specifies the path to the Dockerfile for each custom service.
    -   `image`: Can be used if pulling pre-built images.
    -   `ports`: Maps container ports to host ports (e.g., NGINX 443 to host 443).
    -   `volumes`: Mounts host directories or named volumes into containers for data persistence (e.g., `/home/yourlogin/data/wordpress` and `/home/yourlogin/data/mariadb`).
    -   `networks`: Defines custom networks for inter-container communication.
    -   `env_file`: Points to the `.env` file for loading environment variables.
    -   `restart: always` or `unless-stopped`: Ensures services restart automatically.
-   **Dockerfiles:** Located in service-specific directories (e.g., `srcs/nginx/Dockerfile`).
    -   Define how each service's image is built (base image, dependencies, copying configuration files, entrypoint scripts).
-   **NGINX Configuration (`nginx.conf` or site-specific conf):**
    -   Sets up the server block for your domain.
    -   Configures SSL/TLS (certificates are often self-signed for this project).
    -   Passes PHP requests to the WordPress (PHP-FPM) container.
-   **WordPress Configuration (`wp-config.php`):**
    -   Often set up via environment variables passed to the WordPress container, which then configures it on startup.
    -   Contains database connection details.
-   **Data Volumes:**
    -   WordPress files (themes, plugins, uploads) are stored in a volume (e.g., `wordpress_data`).
    -   MariaDB database files are stored in a volume (e.g., `mariadb_data`).
    -   The subject usually requires these volumes to be located in `/home/<your_login>/data/`.

## 📁 Project Structure

A typical structure for the Inception project:

```
Inception/
├── Makefile
├── docker-compose.yml
├── .env                   # (You create this, gitignored)
└── srcs/
    ├── bonus/             # Optional: For bonus services like Redis, FTP, Adminer etc.
    │   └── ...
    ├── mariadb/
    │   ├── Dockerfile
    │   └── conf/          # MariaDB configuration files, init scripts
    │       └── create_db.sh
    ├── nginx/
    │   ├── Dockerfile
    │   └── conf/          # NGINX configuration files (nginx.conf, site.conf)
    │       └── nginx.conf
    └── wordpress/
        ├── Dockerfile
        └── conf/          # WordPress related scripts or www.conf for php-fpm
            └── wp-config-create.sh # Script to generate wp-config.php
```

## COMMANDS Makefile Targets

Common `Makefile` targets for this project:

-   `all` or `up`: Builds images (if not present or if Dockerfiles changed) and starts all services in detached mode (`docker-compose up --build -d`).
-   `down`: Stops and removes containers, networks defined in `docker-compose.yml`.
-   `clean`: Stops and removes containers, networks. Sometimes also removes untagged images or build cache.
-   `fclean`: Performs `clean` and additionally removes all Docker volumes associated with the project and images built by the compose file. **Use with caution as this deletes your WordPress and MariaDB data.**
-   `re`: Performs `fclean` then `all`. Effectively rebuilds everything from scratch.
-   `logs`: Tails the logs from all services.

## 👨‍💻 Author

**Marouane Aouzal** (Marouane0107)
- GitHub: [@Marouane0107](https://github.com/Marouane0107)
- UM6P-1337 Coding School Student

## 📝 Notes

-   This project is heavily focused on understanding Docker's fundamentals and how services interact in a containerized environment.
-   Pay close attention to volume paths, network configurations, and environment variable usage as per the project subject.
-   Security is a consideration: avoid hardcoding credentials, use `.env` files, and understand basic NGINX security (e.g., SSL).
-   The subject usually specifies exact paths for volumes (e.g., `/home/yourlogin/data/wordpress` and `/home/yourlogin/data/mariadb`).
-   Ensure all Dockerfiles are optimized for image size and build speed where possible (e.g., using `.dockerignore`, multi-stage builds if appropriate, though often not required for the basic project).

---

*Repository for the 42 School "Inception" project.*

*Last Updated: 2024*
