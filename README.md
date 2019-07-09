# Docker template
PHP 7.3 <br>
Mysql 5.7 <br>
Nginx latest

# Set up
In the directory `nginx/conf.d/test.conf` change root to your project directory.
```
server {
    listen 80;
    server_name localhost;

    index index.php index.html;
    error_log  /var/log/nginx/rest-error.log;
    access_log /var/log/nginx/rest-access.log;
    root /var/www/your-project/public;
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
    location / {
        try_files $uri $uri/ /index.php?$query_string;
        gzip_static on;
    }
}
```

**In the `www/` directory you place your php project** <br>

**In the `docker-compose.yaml` you can change the database credentials and database name**

```yaml
db:
    image: mysql:5.7
    container_name: db
    tty: true
    ports:
      - "3307:3306"
    environment:
      MYSQL_DATABASE: your-databasename
      MYSQL_ROOT_PASSWORD: root
      MYSQL_USER: your user
      MYSQL_PASSWORD: your password
      SERVICE_TAGS: dev
      SERVICE_NAME: mysql
    volumes:
      - ./data/dbdata:/var/lib/mysql
      - ./data/mysql/my.cnf:/etc/mysql/my.cnf
    networks:
      - app-network
```