# PS2SMB-Termux

## Persiapan
1. Pertama silahkan update indeks paket menggunakan 
- `apt-get update`
- `apt-get upgrade`
2. Silahkan install proot-distro dengan mengetikkan
- `pkg install proot-distro`
3. Pada case ini akan menggunakan ubuntu karena lebih mudah daripada menggunakan distribusi linux lainnya, silahkan ketikkan 
- `proot-distro install ubuntu`
4. Setelah berhasil terinstal, selanjutnya anda sudah harus menyiapkan direktori/folder untuk menyimpan filesystem OPL-PS2 yang nantinya akan digunakan untuk menyimpan file .iso ataupun VMC (Virtual Memory Card) dsb, sebagai contoh saya memiliki folder PS2SMB yang berada di internal storage Android.
5. Untuk memastikan folder tersebut dapat diakses dari Termux, silahkan ketikkan 
- `termux-setup-storage` terlebih dahulu untuk mengijinkan termux mengakses internal storage android.
<br>Lalu untuk memastikan folder dapat diakses, ketikkan 
- `cd /storage/emulated/0/namafolder` 
sebagai contoh 
- `cd /storage/emulated/0/PS2SMB`

## Konfigurasi Samba Server
1. Pertama, masuk ke session ubuntu dan jangan lupa menambahkan parameter --bind path:path untuk mount folder PS2SMB tadi supaya dapat diakses dari dalam environment proot-distro ubuntu. Karena di dalam environment proot-distro kita tidak dapat mengakses direktori /home ataupun /storage pada environment termux sebelum menambahkan parameter --bind terlebih dahulu ketika pertama kali masuk ke session proot-distro ubuntu.
<br>Silahkan ketikkan
- `proot-distro login ubuntu --bind /storage/emulated/0/PS2SMB:/media/PS2SMB`
2. Setelah berhasil masuk, pastikan folder PS2SMB tadi dapat diakses dari path yang sudah ditentukan tadi yaitu /media/PS2SMB, silahkan ketikkan
- `cd /media/PS2SMB` apabila benar-benar sudah dapat diakses, silahkan lanjutkan ke langkah selanjutnya
3. Seperti biasa
- `apt-get update`
- `apt-get upgrade`
4. Silahkan melakukan instalasi samba menggunakan perintah
- `apt install samba samba-common-bin`
5. Selanjutnya, silahkan edit file konfigurasi samba yang terletak pada /etc/samba/smb.conf menggunakan 2 cara
- __CARA A__, ketikkan <br>
`nano /etc/samba/smb.conf` <br>
Hapus semua konfigurasi dan ganti dengan [smb.conf](https://raw.githubusercontent.com/shinyxn/PS2SMB-Termux/main/smb.conf) yang sudah saya sediakan
- __CARA B__, lebih mudah hanya ketikkan perintah <br>
`curl https://raw.githubusercontent.com/shinyxn/PS2SMB-Termux/main/smb.conf > /etc/samba/smb.conf`
<br><br><b>Notes:</b>
- Port yang digunakan nantinya adalah 1139, sebenarnya default port SMB pada Windows/Linux adalah 445, namun untuk dapat menggunakan port <1024 pada Android harus mempunyai akses root
- File smb.conf yang saya sediakan sudah dikonfigurasi supaya dapat terhubung ke OPL PS2, jujur agak tricky sih untuk koneksikan SMB Server Android via Termux ke PS2, bahkan sampai menggunakan environment proot-distro karena selalu gagal ketika menggunakan samba pada environment termux walaupun sudah merubah smb.conf sedemikian rupa
6. Langkah terakhir, ketikkan
- `testparm` <br>lalu restart samba service menggunakan
- `service smbd restart`

## Koneksi ke PS2
Hubungkan PS2 ke router menggunakan kabel LAN, pastikan Android terhubung ke jaringan yang sama melalui koneksi Wifi.
<br><br>Selanjutnya, cek IP Android menggunakan perintah
- `ifconfig`
![ifconfig](docs/IMG_20230210_000336.png)
<br>
Lalu konfigurasi Network Settings pada OPL sebagai berikut<br>

<br><b>-SMB Server-<br>
Address type : IP<br>
Address : Sesuai IP Android<br>
Port : 1139<br>
Share : PS2SMB<br>
User: guest<br>
Password:</b>

## Penggunaan
Setelah selesai instalasi dan konfigurasi pertama kali, lalu bagaimana cara menjalankan server smb ini lagi selanjutnya?
Pertama masuk ke session ubuntu dan jalankan service smbd
- `proot-distro login ubuntu --bind /storage/emulated/0/PS2SMB:/media/PS2SMB`
- `service smbd start`

## Troubleshooting
Apabila telah selesai mengkonfigurasi samba, jangan lupa untuk merestart service SMB menggunakan 
- `service smbd restart`
