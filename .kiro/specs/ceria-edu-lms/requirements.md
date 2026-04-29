# Requirements Document

## Introduction

CeriaEdu adalah platform LMS (Learning Management System) berbasis web untuk pembelajaran Bahasa Inggris dan Matematika, ditujukan bagi siswa Sekolah Dasar kelas 4–6 (usia ~9–12 tahun). Platform ini dibangun sebagai proyek skripsi dengan fitur utama: autentikasi dua peran (Student & Admin), materi pembelajaran, kuis pilihan ganda, papan peringkat, dan gamifikasi berbasis poin. Admin (guru/pengelola konten) dapat mengelola seluruh konten, sementara siswa mengakses materi, mengerjakan kuis, dan bersaing di leaderboard.

---

## Glossary

- **System**: Platform CeriaEdu secara keseluruhan
- **Auth_Service**: Modul yang menangani autentikasi dan otorisasi pengguna
- **Student**: Pengguna dengan role STUDENT — siswa SD kelas 4–6
- **Admin**: Pengguna dengan role ADMIN — guru atau pengelola konten
- **Material**: Konten pembelajaran berbasis kartu (Card-Based Learning) yang terkait dengan sebuah Subject, disimpan sebagai JSON dengan struktur `{ title, image, cards[] }` di mana setiap item dalam `cards` memiliki `{ type: 'text'|'image'|'heading', value: string }`
- **Card**: Satu unit konten dalam sebuah Material yang dirender sebagai komponen Slide interaktif
- **Game**: Aktivitas interaktif berbasis permainan yang tersedia untuk Student, terdiri dari tiga jenis: Susun Kata, Teka-Teki Kata, dan Puzzle Perkalian & Pembagian
- **Game_Engine**: Modul komponen React murni yang menangani logika dan tampilan setiap jenis Game
- **Quiz**: Kumpulan soal pilihan ganda yang terkait dengan Material atau Subject
- **Question**: Satu butir soal dalam sebuah Quiz, dapat disertai gambar
- **Option**: Pilihan jawaban untuk sebuah Question; tepat satu Option per Question memiliki nilai `isCorrect = true`
- **Submission**: Rekaman hasil pengerjaan Quiz oleh Student, menyimpan skor dan waktu penyelesaian
- **Subject**: Mata pelajaran (Matematika atau Bahasa Inggris)
- **Leaderboard**: Tampilan peringkat Student berdasarkan total poin yang dikumpulkan
- **ActivityLog**: Catatan aktivitas Student (QUIZ atau GAME) beserta poin yang diperoleh
- **Point_Service**: Modul yang menghitung dan memperbarui poin Student setelah aktivitas
- **Content_Manager**: Modul admin untuk mengelola Subject, Material, Quiz, Question, dan Option
- **Cloudinary_Service**: Modul server-side untuk mengunggah dan mengelola aset gambar ke Cloudinary

---

## Requirements

### Requirement 1: Autentikasi Pengguna

**User Story:** Sebagai pengguna (Student atau Admin), saya ingin dapat mendaftar dan masuk ke platform, agar saya dapat mengakses fitur sesuai peran saya.

#### Acceptance Criteria

1. WHEN seorang pengguna mengirimkan formulir registrasi dengan nama, email, dan password yang valid, THE Auth_Service SHALL membuat akun baru dengan role STUDENT secara default dan menyimpan password dalam bentuk hash menggunakan bcryptjs, kemudian menyimpan data pengguna ke database melalui Prisma.
2. WHEN seorang pengguna mengirimkan email dan password yang cocok dengan data di database, THE Auth_Service SHALL memverifikasi kredensial melalui NextAuth.js Credentials provider dan membuat sesi JWT yang menyertakan field `id` dan `role` pengguna.
3. IF seorang pengguna mengirimkan email yang sudah terdaftar saat registrasi, THEN THE Auth_Service SHALL mengembalikan pesan error "Email sudah digunakan".
4. IF seorang pengguna mengirimkan kredensial yang tidak cocok saat login, THEN THE Auth_Service SHALL mengembalikan pesan error "Email atau password salah" tanpa mengungkap field mana yang salah.
5. WHEN seorang pengguna mengakses route yang dilindungi tanpa token yang valid, THE System SHALL mengalihkan pengguna ke halaman login melalui middleware Next.js yang menggunakan `getToken()` dari `next-auth/jwt`.
6. THE System SHALL membedakan akses route berdasarkan role yang tersimpan dalam JWT: route grup `(admin)` hanya dapat diakses oleh Admin, dan route grup `(student)` hanya dapat diakses oleh Student.
7. WHEN seorang pengguna melakukan logout, THE Auth_Service SHALL menghapus sesi aktif dan mengalihkan pengguna ke halaman login.

---

### Requirement 2: Manajemen Konten oleh Admin

**User Story:** Sebagai Admin, saya ingin dapat membuat, mengedit, dan menghapus Subject, Material, Quiz, Question, dan Option, agar konten pembelajaran selalu relevan dan terbarui.

#### Acceptance Criteria

1. THE Content_Manager SHALL menyediakan antarmuka CRUD (Create, Read, Update, Delete) untuk entitas Subject, Material, Quiz, Question, dan Option.
2. WHEN Admin membuat Material baru, THE Content_Manager SHALL mewajibkan pengisian field: subjectId, title, cards, dan order.
3. WHEN Admin membuat Quiz baru, THE Content_Manager SHALL mewajibkan pengisian field: title dan asosiasi ke materialId atau subjectId.
4. WHEN Admin membuat Question baru, THE Content_Manager SHALL mewajibkan minimal 2 Option dan tepat 1 Option dengan nilai `isCorrect = true`.
5. IF Admin mencoba menyimpan Question dengan jumlah Option `isCorrect` tidak sama dengan 1, THEN THE Content_Manager SHALL mengembalikan pesan error validasi sebelum menyimpan ke database.
6. WHEN Admin mengunggah gambar untuk Question atau item `image` dalam `cards` Material, THE Cloudinary_Service SHALL memproses unggahan di sisi server dan menyimpan hanya `public_url` atau `public_id` ke database.
7. WHEN Admin menghapus sebuah Quiz, THE Content_Manager SHALL menghapus seluruh Question dan Option yang berelasi secara cascade.
8. THE Content_Manager SHALL menampilkan daftar seluruh Student beserta total poin dan level mereka kepada Admin.

---

### Requirement 3: Akses Materi Pembelajaran oleh Student

**User Story:** Sebagai Student, saya ingin membaca materi pembelajaran per mata pelajaran dalam bentuk kartu interaktif, agar saya dapat mempersiapkan diri sebelum mengerjakan kuis dengan cara yang menyenangkan.

#### Acceptance Criteria

1. THE System SHALL menampilkan daftar Subject yang tersedia (Matematika dan Bahasa Inggris) kepada Student yang sudah login.
2. WHEN Student memilih sebuah Subject, THE System SHALL menampilkan daftar Material yang terkait, diurutkan berdasarkan field `order` secara ascending.
3. WHEN Student membuka sebuah Material, THE System SHALL merender field `cards` dari Material sebagai komponen Slide/Card interaktif menggunakan Framer Motion untuk animasi transisi antar kartu.
4. THE System SHALL merender setiap item dalam `cards` sesuai dengan field `type`-nya: `heading` sebagai judul, `text` sebagai paragraf, dan `image` sebagai gambar dari URL Cloudinary.
5. WHILE Student membaca Material, THE System SHALL menampilkan tombol navigasi ke Card sebelumnya dan berikutnya di dalam Material yang sama, serta tombol navigasi ke Material sebelumnya dan berikutnya berdasarkan urutan `order`.
6. IF sebuah Material tidak memiliki data `cards` yang tersedia, THEN THE System SHALL menampilkan pesan "Konten belum tersedia" kepada Student.

---

### Requirement 4: Pengerjaan Kuis oleh Student

**User Story:** Sebagai Student, saya ingin mengerjakan kuis pilihan ganda setelah membaca materi, agar saya dapat menguji pemahaman saya dan mendapatkan poin.

#### Acceptance Criteria

1. WHEN Student memulai sebuah Quiz, THE System SHALL menampilkan seluruh Question dalam Quiz tersebut beserta Option-nya satu per satu atau dalam satu halaman.
2. WHILE Student mengerjakan Quiz, THE System SHALL mempertahankan jawaban yang sudah dipilih hingga Student mengirimkan Quiz.
3. WHEN Student mengirimkan Quiz, THE System SHALL menghitung skor berdasarkan jumlah jawaban benar dikalikan poin per Question dan menyimpan hasilnya sebagai Submission baru.
4. WHEN sebuah Submission berhasil disimpan, THE Point_Service SHALL menambahkan poin yang diperoleh ke total poin Student di tabel User dan mencatat aktivitas di ActivityLog dengan type QUIZ.
5. IF Student mencoba mengakses Quiz yang sama setelah memiliki Submission yang sudah selesai (`completedAt` tidak null), THEN THE System SHALL menampilkan hasil Submission sebelumnya dan tidak membuat Submission duplikat.
6. WHEN Student menyelesaikan Quiz, THE System SHALL menampilkan ringkasan hasil: skor yang diperoleh, jumlah jawaban benar, dan total poin yang ditambahkan.
7. IF sebuah Question memiliki gambar, THEN THE System SHALL menampilkan gambar tersebut di atas teks soal menggunakan URL dari Cloudinary.

---

### Requirement 5: Fitur Game Interaktif

**User Story:** Sebagai Student, saya ingin memainkan game interaktif yang berkaitan dengan materi pelajaran, agar saya dapat belajar sambil bermain dan mendapatkan poin tambahan.

#### Acceptance Criteria

**Game 1 — Susun Kata (Word Arrangement)**

1. THE Game_Engine SHALL menampilkan komponen `WordArrangementGame` yang menyajikan kata-kata atau potongan kalimat yang telah diacak kepada Student.
2. WHEN Student menyeret dan melepas kata ke posisi yang benar menggunakan `@dnd-kit`, THE Game_Engine SHALL memperbarui urutan kata secara real-time dengan animasi transisi Framer Motion.
3. WHEN Student berhasil menyusun seluruh kata menjadi kalimat atau kata yang benar, THE Game_Engine SHALL menampilkan animasi umpan balik keberhasilan menggunakan Framer Motion dan AnimatePresence.

**Game 2 — Teka-Teki Kata (Word Puzzle)**

4. THE Game_Engine SHALL menampilkan komponen `WordPuzzleGame` berupa grid huruf yang menyembunyikan kata-kata tersembunyi kepada Student.
5. WHEN Student memilih serangkaian huruf yang membentuk kata tersembunyi yang valid, THE Game_Engine SHALL menyorot kata tersebut dan menandainya sebagai ditemukan.
6. WHEN seluruh kata tersembunyi dalam grid telah ditemukan oleh Student, THE Game_Engine SHALL menampilkan pesan penyelesaian kepada Student.

**Game 3 — Puzzle Perkalian & Pembagian (Multiplication & Division Puzzle)**

7. THE Game_Engine SHALL menampilkan komponen `MultiplicationPuzzleGame` yang menyajikan persamaan matematika tidak lengkap kepada Student.
8. WHEN Student menyeret angka atau operator ke posisi yang kosong dalam persamaan menggunakan `@dnd-kit`, THE Game_Engine SHALL memperbarui tampilan persamaan secara real-time dengan animasi transisi Framer Motion.
9. WHEN Student melengkapi seluruh persamaan dengan benar, THE Game_Engine SHALL menampilkan animasi umpan balik keberhasilan menggunakan Framer Motion dan AnimatePresence.

**Integrasi Poin untuk Semua Game**

10. WHEN Student menyelesaikan salah satu Game, THE Point_Service SHALL memberikan poin kepada Student dengan memanggil mutasi `postActivity` via RTK Query dengan payload `{ type: 'GAME', activityId, points }`.
11. IF Student telah menyelesaikan Game yang sama sebelumnya (entri ActivityLog dengan type GAME dan activityId yang sama sudah ada), THEN THE Point_Service SHALL tidak memberikan poin duplikat untuk penyelesaian ulang tersebut.

### Requirement 6: Leaderboard

**User Story:** Sebagai Student, saya ingin melihat peringkat saya dibandingkan siswa lain, agar saya termotivasi untuk belajar lebih giat.

#### Acceptance Criteria

1. THE System SHALL menampilkan Leaderboard yang memuat daftar Student diurutkan berdasarkan total poin secara descending.
2. THE Leaderboard SHALL menampilkan informasi berikut untuk setiap Student: peringkat, nama, level, dan total poin.
3. WHEN Student membuka halaman Leaderboard, THE System SHALL menyorot baris peringkat Student yang sedang login.
4. THE Leaderboard SHALL menampilkan minimal 10 Student teratas dan memungkinkan Student melihat posisi dirinya sendiri meskipun tidak masuk 10 besar.
5. WHEN total poin seorang Student berubah akibat Submission baru, THE Leaderboard SHALL mencerminkan perubahan peringkat tersebut pada pemuatan halaman berikutnya.

---

### Requirement 7: Sistem Gamifikasi (Poin & Level)

**User Story:** Sebagai Student, saya ingin mendapatkan poin dan naik level setiap kali menyelesaikan aktivitas, agar pengalaman belajar terasa menyenangkan dan memotivasi.

#### Acceptance Criteria

1. WHEN Student menyelesaikan sebuah Quiz, THE Point_Service SHALL menambahkan poin ke total poin Student sesuai dengan skor yang diperoleh dari Submission.
2. WHEN Student menyelesaikan sebuah Game, THE Point_Service SHALL menambahkan poin ke total poin Student dan mencatat entri ActivityLog dengan type GAME.
3. THE Point_Service SHALL menentukan level Student berdasarkan total poin yang dikumpulkan dengan aturan level yang telah ditetapkan (contoh: Level 1 = 0–99 poin, Level 2 = 100–299 poin, Level 3 = 300+ poin).
4. WHEN total poin Student melewati ambang batas level berikutnya, THE Point_Service SHALL memperbarui field `level` pada tabel User secara otomatis.
5. THE System SHALL menampilkan total poin dan level terkini Student di halaman profil dan navigasi utama Student.
6. WHEN sebuah aktivitas Quiz atau Game dicatat, THE Point_Service SHALL menyimpan entri ActivityLog melalui mutasi `postActivity` RTK Query — satu-satunya entry point untuk pencatatan aktivitas — dengan field: userId, type (QUIZ atau GAME), activityId, poin yang diperoleh, dan createdAt.
7. IF Student mengerjakan Quiz yang sama lebih dari satu kali (Submission duplikat), THEN THE Point_Service SHALL tidak menambahkan poin untuk pengerjaan ulang tersebut.
8. IF Student menyelesaikan Game yang sama lebih dari satu kali (ActivityLog dengan type GAME dan activityId yang sama sudah ada), THEN THE Point_Service SHALL tidak menambahkan poin duplikat untuk penyelesaian ulang tersebut.

---

### Requirement 8: Navigasi dan Antarmuka Pengguna Student

**User Story:** Sebagai Student berusia 9–12 tahun, saya ingin antarmuka yang mudah dipahami dan nyaman digunakan di perangkat mobile maupun desktop, agar saya dapat belajar tanpa hambatan teknis.

#### Acceptance Criteria

1. THE System SHALL menampilkan `StudentNavigation` sebagai bottom navigation bar pada layar dengan lebar kurang dari breakpoint `md` Tailwind, dan sebagai top navigation bar pada layar dengan lebar `md` ke atas.
2. THE System SHALL menggunakan ikon dari Lucide-react untuk seluruh elemen navigasi dan aksi utama.
3. THE System SHALL menerapkan desain mobile-first menggunakan kelas Tailwind CSS dengan token dari `@theme` design system.
4. WHILE Student berada di halaman manapun dalam aplikasi, THE System SHALL menampilkan informasi poin dan level Student terkini di area navigasi utama.
5. THE System SHALL memastikan seluruh elemen interaktif (tombol, link, form) dapat diakses dan memiliki ukuran tap target minimal 44x44 piksel untuk kemudahan penggunaan oleh anak-anak.

---

### Requirement 9: Dashboard Admin

**User Story:** Sebagai Admin, saya ingin melihat ringkasan statistik platform, agar saya dapat memantau aktivitas pembelajaran secara keseluruhan.

#### Acceptance Criteria

1. WHEN Admin mengakses halaman dashboard, THE System SHALL menampilkan statistik berikut: total Student terdaftar, total Submission yang telah diselesaikan, dan total Material yang tersedia.
2. THE System SHALL menampilkan `AdminSidebar` sebagai navigasi utama pada antarmuka Admin untuk mengakses seluruh fitur manajemen konten.
3. THE System SHALL memastikan seluruh data statistik pada dashboard Admin diambil dari database secara real-time pada setiap pemuatan halaman.
