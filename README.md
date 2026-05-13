## Reflection

**a. What is amqp?**

AMQP (Advanced Message Queuing Protocol) adalah protokol komunikasi yang digunakan untuk pertukaran pesan antara aplikasi. 
Protokol ini memungkinkan aplikasi untuk mengirim dan menerima message melalui message broker seperti RabbitMQ secara 
asynchronous. Dengan AMQP, publisher dan subscriber dapat saling berkomunikasi tanpa harus terhubung secara langsung.

**b. What does guest:guest@localhost:5672 mean?**

Pada URL "amqp://guest:guest@localhost:5672":

- "guest" pertama merupakan username yang digunakan untuk login ke RabbitMQ.
- "guest" kedua merupakan password dari akun tersebut.
- "localhost" berarti RabbitMQ berjalan di komputer lokal yang sama dengan aplikasi.
- "5672" adalah port default yang digunakan RabbitMQ untuk komunikasi menggunakan protokol AMQP.

### Simulation Slow Subscriber

![Slow Subscriber Queue](./assets/slow-subscriber-queue.png)

Pada percobaan ini, subscriber dibuat menjadi lebih lambat dengan menambahkan delay selama 1 detik pada setiap proses
message menggunakan `thread::sleep()`. Ketika publisher dijalankan berkali-kali secara cepat, publisher terus mengirim
event ke RabbitMQ lebih cepat dibanding subscriber memprosesnya.

Akibatnya, message menumpuk di dalam queue RabbitMQ dan menghasilkan spike pada chart “Queued messages”. Pada 
percobaan saya, jumlah queue sempat mencapai sekitar 35 message sebelum akhirnya perlahan berkurang setelah subscriber 
selesai memproses message satu per satu.

Hal ini menunjukkan fungsi utama message broker dalam event-driven architecture, yaitu menyimpan sementara event
yang belum diproses agar sistem tidak langsung crash ketika beban meningkat. Dengan adanya queue, publisher tetap 
dapat mengirim message tanpa harus menunggu subscriber selesai memproses message sebelumnya.