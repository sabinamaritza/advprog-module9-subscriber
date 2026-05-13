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

![Slow Subscriber Queue](/img/Slow-Subscriber.png)

Pada percobaan ini, subscriber dibuat menjadi lebih lambat dengan menambahkan delay selama 1 detik pada setiap proses
message menggunakan `thread::sleep()`. Ketika publisher dijalankan berkali-kali secara cepat, publisher terus mengirim
event ke RabbitMQ lebih cepat dibanding subscriber memprosesnya.

Akibatnya, message menumpuk di dalam queue RabbitMQ dan menghasilkan spike pada chart “Queued messages”. Pada 
percobaan saya, jumlah queue sempat mencapai sekitar 35 message sebelum akhirnya perlahan berkurang setelah subscriber 
selesai memproses message satu per satu.

Hal ini menunjukkan fungsi utama message broker dalam event-driven architecture, yaitu menyimpan sementara event
yang belum diproses agar sistem tidak langsung crash ketika beban meningkat. Dengan adanya queue, publisher tetap 
dapat mengirim message tanpa harus menunggu subscriber selesai memproses message sebelumnya.

### Running Three Subscribers

![Three Subscribers](/img/Three-Subscribers.png)

![RabbitMQ Monitoring](/img/Three-Subscribers-RabbitMQ.png)

Pada percobaan ini, dijalankan tiga subscriber secara bersamaan yang semuanya terhubung ke queue yang sama di RabbitMQ.
Ketika publisher dijalankan beberapa kali, event yang dikirim tidak lagi diproses oleh satu subscriber saja, tetapi 
dibagi ke beberapa subscriber secara otomatis.

Hal ini terlihat pada console dimana masing-masing subscriber menerima message yang berbeda-beda. Pembagian workload 
tersebut membuat proses konsumsi message menjadi lebih cepat dibanding sebelumnya ketika hanya ada satu subscriber.

Pada dashboard RabbitMQ juga terlihat bahwa spike queue menjadi lebih kecil dan lebih cepat turun kembali ke angka 0. 
Hal ini terjadi karena message diproses secara paralel oleh tiga consumer sekaligus sehingga penumpukan queue 
berkurang.

Event-driven architecture seperti ini sangat membantu untuk meningkatkan scalability dan performa sistem. Ketika 
traffic meningkat, sistem dapat menangani lebih banyak request hanya dengan menambahkan subscriber baru tanpa perlu
mengubah publisher.

Salah satu improvement yang dapat dilakukan pada code publisher dan subscriber adalah membuat konfigurasi queue dan 
connection URL menjadi configurable menggunakan environment variable, sehingga aplikasi lebih fleksibel dan tidak 
bergantung pada hardcoded value seperti `localhost:5672`.