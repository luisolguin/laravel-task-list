---
## Laravel Development Environment with Docker Compose

This project provides a ready-to-use development environment for Laravel applications using Docker Compose. It includes a **MariaDB** database and a **Laravel** application container, making it easy to set up and run your Laravel project without needing to install PHP, a web server, or MariaDB directly on your machine.

---
### Features

* **Isolated Environment**: Your development environment is self-contained within Docker containers, preventing conflicts with your local system.
* **MariaDB Database**: A dedicated MariaDB service for your application data.
* **Laravel Application Container**: Runs your Laravel application using the Bitnami Laravel image.
* **Easy Configuration**: Uses a `.env` file for straightforward environment variable management.
* **Persistent Data**: MariaDB data is persisted using a Docker volume, so your data remains even if containers are removed.

---
### Prerequisites

Before you begin, ensure you have the following installed on your system:

* **Docker Desktop**: Includes Docker Engine and Docker Compose. You can download it from [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop).

---
### Getting Started

Follow these steps to get your Laravel development environment up and running:

#### 1. Clone the Repository

If this `docker-compose.yml` file is part of a larger project, clone your project repository:

```bash
git clone https://github.com/luisolguin/laravel-docker-bitnami.git
cd laravel-docker-bitnami
```

#### 2. Create the `.env` file

Create a file named `.env` in the same directory as your `docker-compose.yml` file. This file will hold your environment variables. Here's an example of what your `.env` file should look like:

```ini
# --- MariaDB Configuration ---
MARIADB_ALLOW_EMPTY_PASSWORD=yes
MARIADB_USER=laravel_user
MARIADB_DATABASE=laravel_db
# MARIADB_ROOT_PASSWORD=your_root_password # Uncomment and set if you need a root password
# MARIADB_PASSWORD=your_db_password       # Uncomment and set if you need a specific password for MARIADB_USER

# --- Laravel Application Configuration ---
LARAVEL_HOST_PORT=8000
LARAVEL_APP_PATH=./src # Path to your Laravel project directory relative to docker-compose.yml

# --- Database Connection for Laravel ---
DB_HOST=mariadb
DB_PORT=3306
DB_USERNAME=laravel_user
DB_DATABASE=laravel_db
# DB_PASSWORD=your_db_password # Uncomment and set if you uncommented MARIADB_PASSWORD above

# --- Docker Volume Configuration ---
MARIADB_VOLUME_NAME=mariadb_data
```

**Important Notes for `.env`:**

* **`LARAVEL_APP_PATH`**: Set this to the relative path of your Laravel application's root directory. For example, if your `docker-compose.yml` is in the root of your project and your Laravel code is in a folder named `src`, you would use `./src`.
* **`MARIADB_ALLOW_EMPTY_PASSWORD`**: For development, `yes` is often sufficient. In production, you should set strong passwords for `MARIADB_ROOT_PASSWORD` and `MARIADB_PASSWORD` and set `MARIADB_ALLOW_EMPTY_PASSWORD=no`.
* **`DB_HOST`**: This should always be `mariadb`, as it refers to the name of the MariaDB service within the Docker network.
* **`DB_USERNAME` and `DB_DATABASE`**: These should match the `MARIADB_USER` and `MARIADB_DATABASE` values defined for the MariaDB service.
* **`DB_PASSWORD`**: If you define `MARIADB_PASSWORD` in your MariaDB configuration, make sure `DB_PASSWORD` in your Laravel configuration matches it.

#### 3. Place your Laravel Application

Ensure your Laravel application's code is located in the directory specified by `LARAVEL_APP_PATH` in your `.env` file. For example, if `LARAVEL_APP_PATH=./src`, your Laravel project files (like `artisan`, `public`, `app`, etc.) should be inside the `src` folder.

#### 4. Build and Run the Containers

Navigate to the directory where your `docker-compose.yml` file is located in your terminal and run:

```bash
docker compose up -d
```

This command will:

* Download the necessary Docker images (`bitnami/mariadb` and `bitnami/laravel`).
* Create a Docker network (`laravel-network-dev`).
* Create a Docker volume (`mariadb_data`) for persistent MariaDB data.
* Start the `mariadb` and `laravel` services in detached mode (in the background).

#### 5. Access your Laravel Application

Once the containers are up and running, your Laravel application should be accessible in your web browser.

* Open your browser and go to `http://localhost:8000` (or whatever port you specified in `LARAVEL_HOST_PORT`).

#### 6. Run Laravel Commands (Optional)

You can execute Laravel Artisan commands inside the `laravel` container. For example, to run migrations:

```bash
docker compose exec laravel php artisan migrate
```

Or to generate an application key:

```bash
docker compose exec laravel php artisan key:generate
```

---
### Stopping the Environment

To stop the running containers without removing them:

```bash
docker compose stop
```

To stop and remove the containers, networks, and volumes (this will **not** delete your MariaDB data, as it's stored in a named volume):

```bash
docker compose down
```

To stop and remove all containers, networks, and the MariaDB data volume (use with caution, as this will delete your database data):

```bash
docker compose down --volumes
```

---
### Project Structure (Example)

```
.
├── docker-compose.yml
├── .env
└── src/
    ├── app/
    ├── bootstrap/
    ├── config/
    ├── database/
    ├── public/
    ├── resources/
    ├── routes/
    ├── storage/
    ├── vendor/
    ├── .env.example
    ├── artisan
    ├── composer.json
    ├── composer.lock
    └── ... (your Laravel project files)
```

---
### Troubleshooting

* **"Error: port is already allocated"**: This means another process on your machine is using the port specified by `LARAVEL_HOST_PORT`. Change `LARAVEL_HOST_PORT` in your `.env` file to a different available port (e.g., `8080`).
* **Database connection issues**:
    * Ensure `DB_HOST` in your `.env` is set to `mariadb`.
    * Verify that `MARIADB_USER`, `MARIADB_DATABASE`, `DB_USERNAME`, and `DB_DATABASE` values match between the MariaDB and Laravel configurations in your `.env` file.
    * If you set a password for MariaDB, ensure `MARIADB_PASSWORD` and `DB_PASSWORD` are uncommented and match in your `.env`.
* **Laravel `APP_KEY`**: If you see errors related to `APP_KEY`, make sure you've run `docker compose exec laravel php artisan key:generate`.
* **Volumes not mapping correctly**: Double-check the `LARAVEL_APP_PATH` in your `.env` file to ensure it points to the correct relative path of your Laravel project.

---
