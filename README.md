# SwiftLing App - Keycloak Docker Compose Setup

## Overview

This repository provides a `docker-compose.yml` file to set up Keycloak with a PostgreSQL database for authentication and authorization within the SwiftLing application.

## Prerequisites

Ensure the following dependencies are installed on your system:

- **Docker** (latest version recommended)
- **Docker Compose**

## Running Keycloak with PostgreSQL

1. Clone the repository:

   ```sh
   git clone https://github.com/CundullahT/swiftling-keycloak-docker-compose.git
   cd swiftling-keycloak-docker-compose
   ```

2. Modify Postgres environment variables in the `docker-compose.yml` file to set up your own database credentials:

   ```yaml
   environment:
     POSTGRES_DB: YOUR_OWN_KEYCLOAK_DB_NAME
     POSTGRES_USER: YOUR_OWN_KEYCLOAK_DB_USERNAME
     POSTGRES_PASSWORD: YOUR_OWN_KEYCLOAK_DB_PASSWORD
   ```

3. Modify Keycloak environment variables in the `docker-compose.yml` file to match your database credentials:

   ```yaml
   environment:
     DB_DATABASE: YOUR_OWN_KEYCLOAK_DB_NAME
     DB_USER: YOUR_OWN_KEYCLOAK_DB_USERNAME
     DB_PASSWORD: YOUR_OWN_KEYCLOAK_DB_PASSWORD
     KC_BOOTSTRAP_ADMIN_USERNAME: YOUR_OWN_KEYCLOAK_DEFAULT_ADMIN_USERNAME
     KC_BOOTSTRAP_ADMIN_PASSWORD: YOUR_OWN_KEYCLOAK_DEFAULT_ADMIN_PASSWORD
   ```

4. Start the containers:

   ```sh
   docker-compose up
   ```

5. Wait until you see the below INFO log message:
   ```
   Keycloak 26.1.4 on JVM (powered by Quarkus 3.15.3.1) started in 5.583s. Listening on: http://0.0.0.0:8080
   ```
   Access Keycloak in your browser:

   ```
   http://localhost:8080
   ```

## Configuring Keycloak for the SwiftLing Application

Once Keycloak is running, follow these steps to configure it for the SwiftLing application:

### 1. Create a New Realm

- Navigate to **Keycloak Admin Console** (`http://localhost:8080/admin`).
- Log in using the admin credentials set in `KC_BOOTSTRAP_ADMIN_USERNAME` and `KC_BOOTSTRAP_ADMIN_PASSWORD`.
- Click on **Create Realm** and enter a name (e.g., `swiftling-realm`).
- Click **Save**.

### 2. Create a New Client

- Inside the new realm, go to **Clients** → **Create**.
- Set **Client ID** (e.g., `swiftling-client`).
- Enable **Client Authentication** to obtain a client secret.
- Configure **Valid Redirect URIs** and **Web Origins** for each microservice and gateway:
  - **Gateway**:
    - **Valid Redirect URI**: `http://localhost:8762/*`
    - **Web Origin**: `http://localhost:8762`
  - **Other Microservices (adjust port numbers accordingly)**:
    - Example for User Service:
      - **Valid Redirect URI**: `http://localhost:8081/*`
      - **Web Origin**: `http://localhost:8081`
    - Example for Phrase Service:
      - **Valid Redirect URI**: `http://localhost:8082/*`
      - **Web Origin**: `http://localhost:8082`
- Click **Save**.

### 3. Create Roles

- Inside the created client, go to \*\*Roles\*\* tab → **Add Role**.
- Create the following roles (case-sensitive):
  - `Root`
  - `Admin`
  - `User`

### 4. Obtain Client Secret

- Navigate to **Your Client** → **Credentials** tab.
- Copy the **Client Secret**.
- Use this secret in all microservices that require a Keycloak client secret for authentication.

## Stopping and Removing Containers

To stop and remove the running containers, execute:

```sh
docker-compose down
```

## Additional Information

- Ensure that each microservice is configured to use the correct **Realm Name** and **Client ID**.
- Each microservice that integrates with Keycloak should set the `realm` in their configurations using the newly created realms name.
- Each microservice that integrates with Keycloak should set the `client-id` in their configurations using the newly created clients id.
- Each microservice that integrates with Keycloak should set the `client-secret` in their configurations using the copied client secret.

## License

This project is licensed under [MIT License](LICENSE).

