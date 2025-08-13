# BookStack Deployment Issue

## Summary

The user is encountering a `502 Bad Gateway` error when trying to access their BookStack instance through a reverse proxy. The BookStack application is containerized using Docker Compose and connects to a separate, containerized database (initially PostgreSQL, then MariaDB). The core issue appears to be the BookStack application failing to properly connect to the database.

## Troubleshooting Steps Attempted

1.  **Initial Setup (PostgreSQL):**
    *   Deployed BookStack and PostgreSQL database using separate `docker-compose.yml` files.
    *   Encountered an error in the BookStack container logs: `could not find driver (Connection: pgsql...)`. This indicated a missing or improperly configured PHP PostgreSQL extension.
    *   Attempted to use the `linuxserver/mods:bookstack-php-pgsql` Docker mod to install the PHP extension. However, the mod failed to install due to network issues or caching problems during container initialization.
    *   Attempted to add the `depends_on` to the docker-compose to ensure the db was running before the app started
    *   Attempted to add a `command` to the application compose file to install the necessary php extension.

2.  **Switch to MariaDB:**
    *   Decided to switch to MariaDB as an alternative database solution.
    *   Modified both `docker-compose.yml` files to use MariaDB.
    *   Updated the BookStack application's database connection settings (driver, host, port, database name, user, password) to match MariaDB.
    *   Encountered a "depends on undefined service" error during `docker-compose up`.

## Current Status

The MariaDB database is confirmed to be running correctly. The "depends on undefined service" error was resolved by deploying the database before deploying the application.
The user is still having issues and has not confirmed resolution.

## Suspected Issues
 * Network isolation: The network may not be configured correctly, with external set to true in the app compose but the network is actually managed in the DB compose file.
 * DB not ready: The DB may not be ready to accept connections, even when the container is running
 * Reverse proxy misconfiguration: The reverse proxy may not be configured correctly.
