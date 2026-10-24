# gRPC Polyglot Lab

Proyek praktikum komunikasi antar-service menggunakan gRPC. Server ditulis dengan
bahasa Go, sedangkan client dibuat dengan Python dan Node.js. Ketiganya berbicara
lewat satu kontrak yang sama, yaitu berkas `user.proto`.

## Struktur Proyek

- `user.proto` : kontrak API (pesan `UserRequest`, `UserResponse`, dan service `UserService`)
- `go-server/` : gRPC server (Go) di port 50051
- `python-client/` : client Python
- `node-client/` : client Node.js

## Cara Menjalankan

1. Server: `cd go-server && go run main.go`
2. Client Python: `cd python-client && py client.py`
3. Client Node.js: `cd node-client && node client.js`

## Analisis: Static Code Generation vs Dynamic Runtime Generation

Pada pendekatan static code generation, yang dipakai oleh Go dan Python, berkas
`.proto` diubah menjadi kode program sebelum aplikasi dijalankan. Kita memanggil
`protoc` (lengkap dengan plugin-nya) pada tahap pengembangan, lalu hasilnya berupa
berkas kode seperti `user.pb.go` dan `user_grpc.pb.go` untuk Go, serta
`user_pb2.py` dan `user_pb2_grpc.py` untuk Python. Berkas hasil generate ini
berisi definisi struct atau class pesan, stub untuk client, dan interface untuk
server, sehingga bisa langsung di-import oleh program. Keuntungannya, kesalahan
tipe data sudah terdeteksi sebelum program jalan (terutama di Go), editor bisa
memberi autocomplete, dan aplikasi tidak perlu membaca skema saat berjalan.
Kelemahannya, setiap kali `.proto` berubah, kita harus menjalankan ulang `protoc`
agar kode hasil generate ikut diperbarui. Hal ini terlihat saat saya menambahkan
field `phone`: kode Go dan Python wajib di-generate ulang. Sebaliknya, pada
dynamic runtime generation yang dipakai Node.js, tidak ada tahap kompilasi
terpisah. Pustaka `@grpc/proto-loader` membaca `user.proto` ketika program mulai
dijalankan, lalu menyusun definisi pesan dan service langsung di memori. Karena
itu, setelah field `phone` ditambahkan, client Node.js cukup diubah kodenya untuk
mencetak `response.phone` tanpa perlu generate ulang. Keuntungannya adalah alur
kerja lebih ringkas dan cocok untuk prototipe cepat. Kelemahannya, salah nama
field baru ketahuan saat program berjalan, ada sedikit tambahan waktu saat startup
untuk memproses skema, dan berkas `.proto` harus tersedia di lokasi program
dijalankan.