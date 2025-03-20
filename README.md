### Referensi
[Final Project: Building a Multithreaded Web Server](https://rust-book.cs.brown.edu/ch20-00-final-project-a-web-server.html)

## Milestone 1: Single-Threaded Web Server

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

## Milestone 2: Returning HTML
Dalam fungsi handle_connection, kita memanfaatkan fs::read_to_string untuk membaca isi dari file hello.html dan menyimpannya dalam bentuk string. Langkah ini memungkinkan server untuk mengirimkan konten HTML sebagai bagian dari respons yang diberikan kepada klien.

Setelah konten HTML diperoleh, kita menyusun respons HTTP dengan menambahkan status line "HTTP/1.1 200 OK", serta menyertakan header Content-Length, yang berfungsi untuk menunjukkan ukuran data yang akan dikirim. Selanjutnya, konten HTML dimasukkan sebagai body dari respons.

Terakhir, respons yang telah dibuat dikirimkan ke klien melalui koneksi TCP stream dengan menggunakan metode write_all, sehingga pengguna dapat menerima dan menampilkan halaman HTML tersebut di browser mereka masing-masing.

![Commit 2 screen capture](/images/commit2.png)

## Milestone 3: Validating Request and Selectively Responding
Sebelumnya, web server selalu menampilkan hello.html tanpa memperhatikan jenis permintaan yang diterima. Dalam tahap ini, kita menambahkan mekanisme untuk memvalidasi permintaan agar hanya memberikan hello.html jika klien meminta halaman /. Jika permintaan berbeda, server akan mengembalikan halaman error 404 beserta kode status 404 Not Found. File notavailable.html dibuat dengan cara yang sama seperti hello.html.

```rust
let request_line = buf_reader.lines().next().unwrap().unwrap();
```
- .lines() → Menghasilkan iterator yang membaca tiap baris dari BufReader.
- .next() → Mengambil baris pertama dari iterator tersebut, yaitu permintaan HTTP (request line).
- unwrap() pertama → Mengambil nilai dari Option<String>, karena .next() bisa mengembalikan None.
- unwrap() kedua → Mengambil nilai dari Result<String, io::Error>, karena .lines() bisa mengalami kesalahan saat membaca.

Agar kode tetap bersih dan terstruktur, saya melakukan refactoring pada main.rs.
Sebelumnya, variabel contents dan status_line dideklarasikan dalam setiap blok if-else, sehingga cakupannya terbatas dan tidak bisa digunakan di luar blok tersebut.

Solusinya, saya menggunakan:

```rust
let (status_line, contents) = ...
```
Dengan cara ini, variabel dapat digunakan secara global dalam fungsi tanpa perlu mendeklarasikan ulang di setiap kondisi if-else. Hal ini meningkatkan keterbacaan dan menjaga efisiensi kode. Maka dari itu refactoring sangat penting.

![Commit 3 screen capture](/images/commit3.png)