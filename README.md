# docker compose file 
## PHPUnit: go to PHPUnit [form chrome](https://www.docker.com/)
In this file you show some image that run as docker 
This image as follows <br> 
:blueberries: sail:
```sh
        image: sail-8.2/app
        extra_hosts:
            - 'host.docker.internal:host-gateway'
        ports:
            - '${APP_PORT:-80}:80'
            - '${VITE_PORT:-5173}:${VITE_PORT:-5173}'
        environment:
            WWWUSER: '${WWWUSER}'
            LARAVEL_SAIL: 1
            XDEBUG_MODE: '${SAIL_XDEBUG_MODE:-off}'
            XDEBUG_CONFIG: '${SAIL_XDEBUG_CONFIG:-client_host=host.docker.internal}'
            IGNITION_LOCAL_SITES_PATH: '${PWD}'
        volumes:
            - '.:/var/www/html'
        networks:
            - vallet-net
        depends_on:
            - mysql
            - redis
```
:blueberries: mysql:
```sh
       image: 'mysql/mysql-server:8.0'
        ports:
            - '${FORWARD_DB_PORT:-3307}:3306'
        environment:
            MYSQL_ROOT_PASSWORD: '${DB_PASSWORD}'
            MYSQL_ROOT_HOST: '%'
            MYSQL_DATABASE: '${DB_DATABASE}'
            MYSQL_USER: '${DB_USERNAME}'
            MYSQL_PASSWORD: '${DB_PASSWORD}'
            MYSQL_ALLOW_EMPTY_PASSWORD: 1
        volumes:
            - 'sail-mysql:/var/lib/mysql'
            - './vendor/laravel/sail/database/mysql/create-testing-database.sh:/docker-entrypoint-initdb.d/10-create-testing-database.sh'
        networks:
            - vallet-net
        healthcheck:
            test:
                - CMD
                - mysqladmin
                - ping
                - '-p${DB_PASSWORD}'
            retries: 3
            timeout: 5s
```
:blueberries: redis:
```sh
        image: 'redis:alpine'
        ports:
            - '${FORWARD_REDIS_PORT:-6379}:6379'
        volumes:
            - 'sail-redis:/data'
        networks:
            - vallet-net
        healthcheck:
            test:
                - CMD
                - redis-cli
                - ping
            retries: 3
            timeout: 5s
```
:blueberries: phpmyadmin:
```sh
        image: phpmyadmin/phpmyadmin
        links:
            - mysql:mysql
        ports:
            - 8081:80
        environment:
            MYSQL_USERNAME: "${DB_USERNAME}"
            MYSQL_ROOT_PASSWORD: "${DB_PASSWORD}"
            PMA_HOST: mysql
        networks:
            - vallet-net
networks:
    vallet-net:
        driver: bridge
volumes:
    sail-mysql:
        driver: local
    sail-redis:
        driver: local

```
:warning: Pay attention before this if docker was up you must use ``` docker container prune``` command in terminal and after that use ``` docker compose up```
