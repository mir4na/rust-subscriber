# A. What is amqp?

AMQP (Advanced Message Queuing Protocol) adalah protokol lapisan aplikasi standar terbuka untuk middleware berorientasi pesan. Protokol ini memungkinkan aplikasi untuk berkomunikasi melalui broker pesan dengan cara mempublikasikan pesan ke exchanges, merutekan pesan ke antrean, dan mengkonsumsi pesan dari antrean. AMQP dirancang untuk memungkinkan pengiriman pesan yang andal, aman, dan interoperabel antar aplikasi yang berbeda. Implementasi populer dari AMQP termasuk RabbitMQ, Apache ActiveMQ, dan Azure Service Bus.

# What does it mean? guest:guest@localhost:5672 , what is the first guest, and what is the second guest, and what is localhost:5672 is for?

String koneksi amqp://guest:guest@localhost:5672 mengikuti format URI AMQP dan dapat diuraikan sebagai berikut. 

- "guest" pertama adalah nama pengguna (username) yang digunakan untuk melakukan autentikasi dengan broker AMQP. Ini merupakan kredensial yang digunakan sistem untuk mengidentifikasi siapa yang mencoba terhubung ke broker pesan. 
- "guest" kedua adalah kata sandi (password) yang digunakan bersama dengan nama pengguna untuk autentikasi ke broker AMQP. Kata sandi ini digunakan untuk memverifikasi identitas pengguna. Bagian "localhost:5672" adalah hostname dan nomor port tempat broker AMQP (seperti RabbitMQ) berjalan dan mendengarkan koneksi. 
- "localhost" menunjukkan bahwa broker pesan berjalan di mesin lokal yang sama dengan aplikasi, dan "5672" adalah nomor port default untuk koneksi AMQP. Perlu dicatat bahwa kredensial "guest:guest" adalah default untuk banyak broker AMQP seperti RabbitMQ, tetapi biasanya dibatasi hanya untuk koneksi lokal demi alasan keamanan.

# Simulation slow subscriber

![alt text](image.png)

Pada grafik terlihat sekitar 50-60 pesan yang tertumpuk dalam antrian, hal ini terjadi karena subscriber memproses setiap pesan dengan delay 1 detik (1000ms), sementara publisher mampu mengirim pesan lebih cepat. Ketidakseimbangan antara kecepatan pengiriman pesan yang tinggi dari publisher dengan kecepatan pemrosesan yang lebih lambat di subscriber inilah yang menyebabkan penumpukan pesan dalam queue. Semakin lama publisher dijalankan berulang kali, semakin banyak pula pesan yang akan menumpuk karena subscriber tidak mampu mengimbangi kecepatan pengiriman.

# Reflection and Running at least three subscribers

![alt text](image-3.png)
![alt text](image-4.png)

Dengan menjalankan 3 subscribers secara bersamaan, sistem sekarang mampu memproses pesan 3 kali lebih cepat karena beban kerja didistribusikan secara merata ke semua consumer. Penurunan pesan yang signifikan terlihat pada grafik karena setiap subscriber bekerja paralel menangani pesan berbeda secara bersamaan, berbeda dengan sebelumnya yang hanya mengandalkan satu consumer. Namun, perlu diperhatikan bahwa penambahan consumer ini memiliki batas optimal - jika melebihi kapasitas sistem justru bisa menurunkan performa karena overhead manajemen thread. Solusi ini efektif untuk kasus dimana processing time per pesan konsisten (1 detik), tetapi untuk sistem real-world perlu ditambahkan mekanisme error handling dan pengaturan prefetch count yang lebih baik.