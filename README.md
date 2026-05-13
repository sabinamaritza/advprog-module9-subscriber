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