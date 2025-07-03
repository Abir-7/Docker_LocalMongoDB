# MongoDB Replica Set Setup with Docker Compose

This guide explains how to set up a MongoDB replica set locally using Docker Compose.

---

## Steps

1. **Create a Docker network for the replica set:**

    ```bash
    docker network create mongo-cluster
    ```

2. **Start the MongoDB containers:**

    ```bash
    docker-compose up -d
    ```

3. **Connect to the primary MongoDB container:**

    ```bash
    docker exec -it mongo1 mongosh
    ```

4. **Initiate the replica set inside the `mongosh` shell:**

    ```js
    rs.initiate({
      _id: "rs0",
      members: [
        { _id: 0, host: "mongo1:27017" },
        { _id: 1, host: "mongo2:27017" },
        { _id: 2, host: "mongo3:27017" }
      ]
    })
    ```

5. **Check replica set status:**

    ```js
    rs.status()
    ```
    Look for `"stateStr" : "PRIMARY"` to find your primary database.

---

## Connection Strings

- **Application (.env) URI:**
    ```
    mongodb://mongo1:27017,mongo2:27017,mongo3:27017/<db_name>?replicaSet=rs0
    ```

- **MongoDB Compass URI:**
    ```
    mongodb://localhost:<primary-db-port>/?directConnection=true
    ```
    Replace `<primary-db-port>` with the port mapped for the primary (default is 27017).

---

## docker-compose.yml Example - for Express server to conncet local mongodb

```yaml
version: "3.9"

services:
  sass-rabbitmq:
    image: rabbitmq:3-management
    container_name: sass-solution
    ports:
      - "5673:5672"  # Changed left port to avoid conflict with old project
      - "15673:15672" # Changed left port to avoid conflict with old project
    environment:
      RABBITMQ_DEFAULT_USER: ${RABBITMQ_DEFAULT_USER}
      RABBITMQ_DEFAULT_PASS: ${RABBITMQ_DEFAULT_PASS}
    networks:
      - mongo-cluster

  sass-express:
    build: .
    depends_on:
      - sass-rabbitmq
    env_file:
      - .env
    ports:
      - "4002:4000" # Changed left port to avoid conflict with old project
    volumes:
      - .:/app
    command: npm run dev
    networks:
      - mongo-cluster


networks:
  mongo-cluster:
    external: true
