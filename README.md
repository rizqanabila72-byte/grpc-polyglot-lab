# gRPC Polyglot Lab

Praktikum komunikasi inter-service dengan gRPC. Server ditulis dalam Go,
client dalam Python dan Node.js, semuanya memakai kontrak `user.proto`.

## Analisis Pendekatan Kompilasi: Static vs Dynamic

Pada pendekatan static code generation (Go dan Python), berkas .proto diproses
terlebih dahulu oleh compiler protoc pada saat pengembangan. Hasilnya adalah
berkas kode sumber nyata (user.pb.go, user_grpc.pb.go, user_pb2.py,
user_pb2_grpc.py) yang berisi struct atau class pesan, stub client, dan interface
server. Keunggulannya adalah pengecekan tipe sejak compile time, dukungan
autocomplete IDE, dan tidak ada proses parsing skema saat program berjalan.
Kekurangannya, setiap perubahan .proto mengharuskan kita menjalankan ulang protoc.
Sebaliknya, pada dynamic runtime generation (Node.js), tidak ada langkah compile.
Pustaka @grpc/proto-loader membaca user.proto saat program dijalankan lalu
membentuk definisi pesan dan service secara dinamis di memori. Keunggulannya
adalah alur kerja yang sederhana karena perubahan skema langsung terbaca tanpa
generate ulang. Kekurangannya, kesalahan nama field baru ketahuan saat runtime,
ada sedikit overhead saat startup, dan berkas .proto harus tersedia saat program
berjalan.
