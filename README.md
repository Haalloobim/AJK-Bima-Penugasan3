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



