# BookStack Deployment Issue - Troubleshooting Log

## Summary

I'm experiencing a `502 Bad Gateway` error when trying to access my BookStack instance through a reverse proxy. I've containerized BookStack and a database using Docker Compose, and I'm struggling to get the application to connect to the database.

## Troubleshooting Steps

1.  **Initial Setup (PostgreSQL):**
    *   I set up BookStack and a PostgreSQL database using separate `docker-compose.yml` files.
    *   The BookStack container logs showed a "could not find driver (Connection: pgsql...)" error, indicating a problem with the PHP PostgreSQL extension.
    *   I tried using the `linuxserver/mods:bookstack-php-pgsql` Docker mod to install the extension. However, the mod failed to install, likely due to network issues during container creation. The logs showed an OFFLINE error.
    *   I tried adding `depends_on` to the docker-compose file to ensure the database was running before the app started.
    *   I tried adding a `command` to the application compose file to install the necessary PHP extension directly during container startup. This also did not resolve the issue.

2.  **Switch to MariaDB:**
    *   I decided to switch to MariaDB as an alternative.
    *   I modified both `docker-compose.yml` files to use MariaDB.
    *   I updated the BookStack application's database connection settings (driver, host, port, database name, user, password) to match MariaDB.
    *   I encountered a "depends on undefined service" error during `docker-compose up`. This was because I had the database defined in a separate compose file and was referencing it in the application compose file.

## Current Status

The MariaDB database is running. I resolved the "depends on undefined service" error by first deploying the database, and then deploying the application.

## Suspected Issues

*   **Network configuration:** The network configuration might be incorrect. I have an external network defined in the app compose file, but the network is actually managed in the database compose file.
*   **Database readiness:** The database might not be fully ready to accept connections even when the container is running. There could be a delay that BookStack isn't accounting for.
*   **Reverse proxy:** There could be a problem with the reverse proxy configuration.
