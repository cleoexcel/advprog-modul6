
## Commit 1 Reflection
### Milestone 1: Single-Threaded Web Server

Dalam pembuatan single-threaded web server, terdapat dua protokol utama yang berperan, yaitu Hypertext Transfer Protocol (HTTP) dan Transmission Control Protocol (TCP). Kedua protokol ini bekerja berdasarkan prinsip request-response, di mana server menerima permintaan dari klien dan kemudian memberikan respons yang sesuai.

TCP merupakan protokol tingkat rendah yang menentukan bagaimana data dikirim dari satu server ke server lainnya, tetapi tidak mendefinisikan isi dari data tersebut. Sementara itu, HTTP berfungsi untuk menentukan format serta isi dari permintaan (request) dan respons (response) yang dikirim melalui koneksi TCP.

Pada tahap awal pengembangannya, program ini hanya dapat menerima permintaan dari browser dengan menggunakan TcpListener yang terhubung ke alamat 127.0.0.1:7878. Setiap kali ada koneksi yang masuk, program akan menampilkan pesan Connection established! di konsol.

Beberapa fungsi utama yang digunakan dalam program ini adalah:

- bind(): Menghubungkan TcpListener ke alamat dan port tertentu pada perangkat lokal, sehingga server dapat mulai mendengarkan koneksi yang masuk.
- unwrap(): Mengambil nilai dari objek Result atau Option. Jika hasilnya adalah Ok atau Some, maka nilai di dalamnya akan dikembalikan. Namun, jika hasilnya Err atau None, program akan mengalami panic dan berhenti karena terjadi kesalahan yang tidak tertangani.
- incoming(): Menghasilkan iterator yang menyediakan stream dari setiap koneksi yang diterima oleh TcpListener. Dengan menggunakan iterator ini, server dapat menangani koneksi secara berurutan.
Agar server dapat memproses permintaan dari browser dengan lebih baik, dibuatlah fungsi handle_connection(). Pada akhir eksekusi fungsi ini, program akan mencetak permintaan HTTP yang diterima ke konsol.

Dalam fungsi handle_connection(), beberapa elemen penting yang digunakan meliputi:

- BufReader: Digunakan untuk membaca data dari TcpStream secara efisien dalam bentuk baris per baris menggunakan metode lines().
- http_request: Menyimpan kumpulan baris yang membentuk permintaan HTTP yang dikirim oleh klien/browser, sebelum kemudian diproses oleh server.