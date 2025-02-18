# Metabase with PostgreSQL - Docker Compose Setup

This repository contains a `docker-compose.yaml` file that sets up Metabase with a PostgreSQL database using Docker Compose.

## Prerequisites
- Docker installed on your system
- Docker Compose installed

## Services Overview
This setup includes the following services:

### Metabase
Metabase is an open-source business intelligence tool that allows you to explore and visualize your data.
- Runs the latest Metabase image.
- Exposes Metabase on port `3000`.
- Uses PostgreSQL as the backend database.
- Configured with a health check to ensure availability.

### PostgreSQL
PostgreSQL is used as the database for Metabase.
- Runs the latest PostgreSQL image.
- Stores persistent data in a local volume (`./postgres-data`).
- Uses `metabase` as the default user and `metabaseappdb` as the database name.
- Configured with a health check to ensure availability.

## Configuration Details
The `docker-compose.yaml` file defines the services as follows:

### Metabase Service (`metabase`)
```yaml
docker-compose.yaml contains services:
  metabase:
    image: metabase/metabase:latest
    container_name: metabase
    hostname: metabase
    restart: always
    depends_on:
      postgres:
        condition: service_healthy
    volumes:
      - /dev/urandom:/dev/random:ro
    ports:
      - 3000:3000
    environment:
      MB_DB_TYPE: postgres
      MB_DB_DBNAME: metabaseappdb
      MB_DB_PORT: 5432
      MB_DB_USER: metabase
      MB_DB_PASS: mysecretpassword
      MB_DB_HOST: postgres
    networks:
      - metanet1
    healthcheck:
      test: curl --fail -I http://localhost:3000/api/health || exit 1
      interval: 15s
      timeout: 5s
      retries: 5
```

### PostgreSQL Service (`postgres`)
```yaml
  postgres:
    image: postgres:latest
    container_name: postgres
    hostname: postgres
    restart: always
    volumes:
      - ./postgres-data:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: metabase
      POSTGRES_DB: metabaseappdb
      POSTGRES_PASSWORD: mysecretpassword
    networks:
      - metanet1
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U metabase"]
      interval: 15s
      timeout: 5s
      retries: 5
```

### Networks
```yaml
networks:
  metanet1:
    driver: bridge
    name: metanet1
```

## How to Use
1. Clone this repository:
   ```sh
   git clone <repository_url>
   cd <repository_folder>
   ```
2. Start the services:
   ```sh
   docker-compose up -d
   ```
3. Access Metabase in your browser:
   ```
   http://localhost:3000
   ```
4. Log in and configure Metabase using PostgreSQL credentials defined in `docker-compose.yaml`.

## Stopping and Removing Containers
To stop and remove the running containers:
```sh
docker-compose down
```

## Persistent Data
PostgreSQL data is stored in the `./postgres-data` directory. This ensures that database data is not lost when restarting the containers.

## Troubleshooting
- If you experience issues, check the logs:
  ```sh
  docker-compose logs -f
  ```
- Ensure that Docker and Docker Compose are installed and running.

## License
This project is open-source and can be modified as needed.

## Author
Zulal Basoner

