# Repository Penugasan Ketiga OPREC AJK 2024 [CI/CD]

## By: Muhammad Bimatara Indianto / 5025221260

## Deskripsi Penugasan Ketiga
Pada penugasan ketiga kali ini, Camin diperintahkan untuk melakukan proses deploy pada suatu ___laravel-app___ dengan menggunakan sebuah ___cloud service___. Pada prosesi deploy, camin diharuskan untuk menggunakan ___github action___ untuk mengimplementasi ___CI/CD___ sehingga pada saat melakukan push perubahan pada git akan terotomasi untuk proses deploy. Tujuan utama dari penugasan ketiga kali ini adalah Laravel-app tersebut dapat di deploy pada cloud service dan melakukan otomasi pada proses build dan dapat melakukan caching dalam proses tersebut. 

### Repository Source
- [Laravel App](https://github.com/mvinorian/tamiyochi-laravel)

## How to Solve?
Pada pengerjaan penugasan kali ini, terdapat beberapa langkah yang harus saya lakukan untuk medapatkan hasil akhir yaitu mendeploy secara otomatis melalui cloud service dan github action sebagai berikut:

- Melakukan deploy manual di localhost tanpa menggunakan nginx, mengikuti cara yang diberikan pada github tamiyochi. Setelah berhasil melakukan langkah ini yaitu melakukan doploy di localhost, saya melakukan langkah berikutnya. 
- Melakukan deploy manual di localhost menggunakan Dockerfile (Menyimpan Image Laravel APP) dan juga menggunakan docker compose untuk menyambungkan koneksi pada MySQL. Setelah berhasil, saya melakukan langkah berikutnya.
- Melakukan deploy manual di localhost menggunakan Dockerfile (Menyimpan Image Laravel APP) dan juga menggunakan docker compose untuk menyambungkan koneksi pada container MySQL, sekaligus menggunakan container NGINX sebagai port fowarding agar bisa di deploy. Setelah berhasil, saya melakukan langkah berikutnya.
- Melakukan deploy pada cloud service secara manual dan mencoba apakah benar dapat bisa diakses pada ip public.  Setelah berhasil, saya melakukan langkah berikutnya.
- Dan yang terakhir melakukan deploy menggunakan github action. 

Pada saat melakukan compose up untuk proses deploy, saya menggunakan __4 Services__ yang berbeda dan dihubungkan menggunakan __1 Network__ kemudian membuat satu shared __1 Shared Volumes__.  Berikut merupakan services yang saya gunakan di [docker-compose.yml](./docker-compose.yml):

- Service MySQL -> menggunakan base image mysql:5.7
- Service Nginx -> menggunakan base image nginx:alpine
- Service Laravel-App -> menggunakan base image dari [Dockerfile](./src/Dockerfile) yang sudah dibuat sebelumnya _*akan dijelaskan lebih lanjut_
- Service Migration -> menggunakan base image dari [Dockerfile](./src/Dockerfile). Container ini bertujuan untuk melakukan otomasi pada proses migration. 

Berikut merupakan config dari services MySQL yang saya gunakan. 
```yaml
version: '3.7'
services:
  MySQL:
    image: mysql:5.7
    container_name: laravelMySQL
    restart: on-failure
    ports:
      - "3306:3306"
    networks:
      - HaallooNet
    environment:
      MYSQL_ROOT_PASSWORD: ''
      MYSQL_CONNECTION: 'mysql'
      MYSQL_HOST: 'localhost'
      MYSQL_DATABASE: 'pbkk'
      MYSQL_PASSWORD: ''
      MYSQL_ALLOW_EMPTY_PASSWORD: true
    healthcheck:
      test: ["CMD", "mysqladmin" ,"ping", "-h", "localhost"]
      interval: 20s
      timeout: 10s
      retries: 3
      start_period: 3s
```
Pada service ini terdapat beberapa atribut, antara lain adalah sebagai berikut:

- `image`: berfungsi untuk memanggil image yang akan digunakan. Disini saya menggunakan image `mysql:5.7`
- `container_name`: berfungsi untuk memberi nama container/service yang digunakan. 
- `restart`: berfungsi sebagai pemberi signal untuk melakukan reset container. Disini saya menggunakan on_failure yang artinya hanya akan melakukan restarting container ketika terjadi sebuah kegagalan. 
- `ports`: berfungsi untuk menentukan pada port mana service/container akan berjalan. Disini saya menggunakan 3306 *_Default MySQL port_
- `networks`: berfungsi untuk menentukan network apa yang digunakan untuk menjembatani semua services yang ada pada docker-compose.yml. Disini saya menggunakan `HaallooNet`, networks yang saya buat. 
- `environment`: berfungsi sebagai penampung _environemnt variable_ yang akan digunakan dalam sebuah container. Disini saya menggunakan data data tersebut sebagai hal yang diperlukan ketika menggunakan MySQL image. 
- `Health Check`: berfungsi untuk menghasilkan kondisi baru apakah container yang bersangkutan dalam keadaan yang diharapkan atau tidak dan juga bisa mengirimkan kondisi tersebut ke container/service lain untuk melakukan pengkondisian. Untuk health check disini saya melakukan proses `ping` ke mysql admin yang bisa menandakan bahwa container sekaligus server mysql nya sudah berjalan dengan baik. 

Kemudian Berikut merupakan config dari services BE yang saya gunakan.

```yml
version: '3.7'
services:
    BE:
        image: haalloobim/be-laravel:latest
        container_name: laravelBackendApp
        restart: on-failure
        ports:
            - "9000:9000"
        networks:
            - HaallooNet
        depends_on:
            MySQL:
                condition: service_healthy
        environment:
            DB_CONNECTION: mysql
            DB_HOST: MySQL
            DB_PORT: 3306
            DB_DATABASE: pbkk
            DB_USERNAME: 'root'
            DB_PASSWORD: ''
        healthcheck:
            test: ["CMD", "ping", "-c", "1", "MySQL"]
            interval: 20s
            timeout: 10s
            retries: 5
            start_period: 3s
        volumes:
            - connected-volume:/var/www/html
```
Pada service ini terdapat beberapa atribut, antara lain adalah sebagai berikut:

- `image`: berfungsi untuk memanggil image yang akan digunakan. Disini saya menggunakan image `haalloobim/be-laravel:latest`
- `container_name`: berfungsi untuk memberi nama container/service yang digunakan. 
- `restart`: berfungsi sebagai pemberi signal untuk melakukan reset container. Disini saya menggunakan `on_failure` yang artinya hanya akan melakukan restarting container ketika terjadi sebuah kegagalan. 
- `ports`: berfungsi untuk menentukan pada port mana service/container akan berjalan. Disini saya menggunakan 9000 
- `networks`: berfungsi untuk menentukan network apa yang digunakan untuk menjembatani semua services yang ada pada docker-compose.yml. Disini saya menggunakan `HaallooNet`, networks yang saya buat. 
- `depends_on`: berfungsi untuk menunggu service yang di depend kan berhasil melakukan apa yang dia kerjakan. Pada kasus ini, service ini akan menunggu hingga status dari service yang dituju menjadi healthy. 
- `environment`: berfungsi sebagai penampung _environemnt variable_ yang akan digunakan dalam sebuah container. Disini saya menggunakan data data tersebut sebagai hal yang diperlukan ketika menggunakan image tersebut. 
- `Health Check`: berfungsi untuk menghasilkan kondisi baru apakah container yang bersangkutan dalam keadaan yang diharapkan atau tidak dan juga bisa mengirimkan kondisi tersebut ke container/service lain untuk melakukan pengkondisian. Untuk health check disini saya melakukan proses `ping` ke container MySQL yang bisa menandakan bahwa container MySQL nya sudah berjalan dengan baik. 
- `volumes`: berfungsi untuk menampung volumes yang dibutuhkan oleh container.

Kemudian Berikut merupakan config dari services BE-Migration yang saya gunakan.

```yml
version: '3.7'
services:
    BE-Migration:
        image: haalloobim/be-laravel:latest
        container_name: laravelBackendMigration
        restart: on-failure
        networks:
            - HaallooNet
        depends_on:
            MySQL:
                condition: service_healthy
        command: php artisan migrate --seed
```
Pada service ini terdapat beberapa atribut, antara lain adalah sebagai berikut:

- `image`: berfungsi untuk memanggil image yang akan digunakan. Disini saya menggunakan image `haalloobim/be-laravel:latest`
- `container_name`: berfungsi untuk memberi nama container/service yang digunakan. 
- `restart`: berfungsi sebagai pemberi signal untuk melakukan reset container. Disini saya menggunakan `on_failure` yang artinya hanya akan melakukan restarting container ketika terjadi sebuah kegagalan. 
- `networks`: berfungsi untuk menentukan network apa yang digunakan untuk menjembatani semua services yang ada pada docker-compose.yml. Disini saya menggunakan `HaallooNet`, networks yang saya buat. 
- `depends_on`: berfungsi untuk menunggu service yang di depend kan berhasil melakukan apa yang dia kerjakan. Pada kasus ini, service ini akan menunggu hingga status dari service yang dituju menjadi healthy. 

Kemudian Berikut merupakan config dari services nginx yang saya gunakan.

```yml
version: '3.7'
services:
    nginx:
        image: nginx:alpine
        container_name: nginx
        restart: on-failure
        ports:
            - "80:80"
        depends_on: 
            - BE
        networks: 
            - HaallooNet
        volumes:
            - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
            - connected-volume:/var/www/html
```
Pada service ini terdapat beberapa atribut, antara lain adalah sebagai berikut:

- `image`: berfungsi untuk memanggil image yang akan digunakan. Disini saya menggunakan image `nginx:alpine`
- `container_name`: berfungsi untuk memberi nama container/service yang digunakan. 
- `restart`: berfungsi sebagai pemberi signal untuk melakukan reset container. Disini saya menggunakan `on_failure` yang artinya hanya akan melakukan restarting container ketika terjadi sebuah kegagalan. 
- `ports`: berfungsi untuk menentukan pada port mana service/container akan berjalan. Disini saya menggunakan `80`
- `networks`: berfungsi untuk menentukan network apa yang digunakan untuk menjembatani semua services yang ada pada docker-compose.yml. Disini saya menggunakan `HaallooNet`, networks yang saya buat. 
- `depends_on`: berfungsi untuk menunggu service yang di depend kan berhasil melakukan apa yang dia kerjakan. Pada kasus ini, service ini akan menunggu hingga status dari service yang dituju menjadi healthy. 
- `volumes`: berfungsi untuk menampung volumes yang dibutuhkan oleh container.

Berikut juga akan saya lampirkan file Dockerfile yang saya buat untuk membuat image. 

```Dockerfile
FROM php:8.2-fpm-alpine

RUN apk update && apk add libpng-dev zip unzip curl libxml2-dev git nano

RUN docker-php-ext-install pdo pdo_mysql \
    && apk --no-cache add nodejs npm

RUN npm install -g yarn

WORKDIR /var/www/html

COPY . /var/www/html

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN composer install

RUN chmod -R 777 .

RUN chown -R www-data:www-data .

RUN php artisan key:generate && php artisan storage:link && yarn

RUN yarn build
```

Disini saya menggunakan referensi sama seperti waktu ketika Praktikum Modul 3 Sistem Operasi dan menambahkan sedikit tambahan yaitu pada bagian installing yarn dan proses build dari yarn tersebut. 

Untuk Github action yang saya gunakan kurang lebih berfungsi sebagai untuk melakukan hal hal berikut:

Login Docker -> Melakukan Build image dari Gtihub -> Melakukan push ke github repository -> mengcopy file file penting kedalam VM instance menggunakan appleboy action -> melakukan connect ssh menggunakan ssh appleboy action -> Melalukan pull image dari docker hub -> Melakukan docker compose up

Berikut adalah source code dari github action yang saya gunakan: 

```yml
name: File Change Handling on Docker Env

on: 
  push:
    branches: 
      - main
    paths:
      - 'src/**'  
      - '.github/workflows/**'
      - docker-compose.yml
      - nginx/default.conf
  workflow_dispatch:

jobs:
  Job-Testing:
    runs-on: ubuntu-latest
    steps: 
        - name: Bash Script Test
          run: echo "Testing Job.." 
          shell: bash  

  Docker-Build-Push:
    runs-on: ubuntu-latest
    steps:
        - name: Checkout
          uses: actions/checkout@v4

        - name: Set up QEMU
          uses: docker/setup-qemu-action@v3
       
        - name: Set up Docker Buildx
          uses: docker/setup-buildx-action@v3
        
        - name: Create .env file
          run: |
           touch ./src/.env
           echo "${{secrets.ENV_FILE}}" > ./src/.env
          shell: bash

        - name: Login to Docker Hub
          uses: docker/login-action@v2
          with:
            username: ${{ secrets.DOCKER_USER }}
            password: ${{ secrets.DOCKER_PASS }}

        - name: Build and push
          uses: docker/build-push-action@v5
          with:
            context: ./src/
            push: true
            tags: ${{ secrets.DOCKER_USER }}/be-laravel:latest
            cache-from: type=gha
            cache-to: type=gha,mode=max
        
        - name: Runner Health Check
          if: success()
          run: echo "Docker Build and Push Completed.."
          shell: bash

  Docker-Deploy:
    runs-on: ubuntu-latest
    needs: Docker-Build-Push
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Copying NGINX Conf Files to SSH
        uses: appleboy/scp-action@v0.1.7
        with:
          host: ${{ secrets.SSH_HOST }}
          username: ${{ secrets.SSH_USER }}
          port: ${{ secrets.SSH_PORT }}
          key: ${{ secrets.SSH_PRIV_KEY }}
          source: "nginx/default.conf,docker-compose.yml"
          target: ajk-cicd/
      
      - name: Remove and Pulling New Image and Compose Up
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.SSH_HOST }}
          username: ${{ secrets.SSH_USER }}
          port: ${{ secrets.SSH_PORT }}
          key: ${{ secrets.SSH_PRIV_KEY }}
          script: |
            docker compose down
            docker rmi ${{ secrets.DOCKER_USER }}/be-laravel:latest
            docker pull ${{ secrets.DOCKER_USER }}/be-laravel:latest
            cd ajk-cicd
            docker compose up -d

      - name: Runner Health Check
        if: success()
        run: echo "Docker-Deploy Completed..."
        shell: bash
            
```
Pada github action ini juga, saya hanya akan menjalankan action ketika file file berikut mengalami perubahan: 

- src/**  
- .github/workflows/**
- docker-compose.yml
- nginx/default.conf

dan juga melakukan `actions/checkout@v4` untuk mengetahui kondisi atau directory yang ada di github ini. 
