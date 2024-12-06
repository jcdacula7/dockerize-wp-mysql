# Dockerize WordPress
Dockerizing WordPress involves setting up a containerized environment for both WordPress and its database. 


## Prerequisites
1. Install Docker Desktop -
Ensure Docker Desktop is installed on your system.
2. Basic Understanding of Docker -
Familiarity with Docker concepts like images, containers, and Docker Compose.


## Step 1: Create a Project Directory
1. Open a terminal.
2. Create a directory for your project:
```bash
mkdir dockerized-wordpress
cd dockerized-wordpress
```

## Step 2: Set Up a `docker-compose.yml` File
1. In your project directory, create a `docker-compose.yml` file:
```bash
touch docker-compose.yml
```

2. Open the file in a text editor and add the following configuration:
```bash
services:
  database:
    image: mysql:latest
    container_name: mysql-container
    restart: unless-stopped
    ports:
      - 3307:3306
    env_file: .env
    environment:
      MYSQL_DATABASE: "${MYSQL_DATABASE}"
      MYSQL_USER: "${MYSQL_USER}"
      MYSQL_PASSWORD: "${MYSQL_PASSWORD}"
      MYSQL_ROOT_PASSWORD: "${MYSQL_ROOT_PASSWORD}"
    volumes:
      - db-data:/var/lib/mysql
    networks:
      - wordpress-network

  phpmyadmin:
    depends_on:
      - database
    image: phpmyadmin/phpmyadmin
    container_name: phpmyadmin-container
    restart: unless-stopped
    ports:
      - 8881:80
    env_file: .env
    environment:
      PMA_HOST: database
      MYSQL_ROOT_PASSWORD: "${MYSQL_ROOT_PASSWORD}"
    networks:
      - wordpress-network

  wordpress:
    depends_on:
      - database
    image: wordpress:latest
    container_name: wordpress-container
    restart: unless-stopped
    ports:
      - 8880:80
    env_file: .env
    environment:
      WORDPRESS_DB_HOST: database:3306
      WORDPRESS_DB_NAME: "${MYSQL_DATABASE}"
      WORDPRESS_DB_USER: "${MYSQL_USER}"
      WORDPRESS_DB_PASSWORD: "${MYSQL_PASSWORD}"
    volumes:
      - ./wp-content/:/var/www/html/wp-content
    networks:
      - wordpress-network

volumes:
  db-data:

networks:
  wordpress-network:
    driver: bridge
```


## Step 3: Start the Containers
Run the following command in your project directory to start the services:
```bash
docker-compose up -d
```
* `-d` runs the containers in detached mode (in the background).



## Step 4: Verify the Setup
1. Open a web browser.
2. Navigate to `http://localhost:8080`.
3. Follow the WordPress setup wizard.



## Step 5: (Optional) Manage Your Containers
* To view running containers:
```bash
docker ps
```

* To stop the containers:
```bash
docker-compose down
```

* To rebuild and apply changes:
```bash
docker-compose up -d --build
```


## Step 6: (Optional) Persist Data
The `volumes` defined in `docker-compose.yml` ensure that WordPress and database data persist between container restarts. You can verify this by stopping and restarting the containers without losing data.



## Step 7: Customize the Environment
You can customize:
* **Ports:** Change `8080:80` if port `8080` is already in use.
* **Database Credentials:** Update environment variables in `docker-compose.yml`.



## Conclusion
You now have a fully dockerized WordPress environment. This setup is portable, easy to manage, and can be quickly deployed across different environments.
