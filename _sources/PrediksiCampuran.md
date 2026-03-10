# Prediksi Missing Value Data Kategorikal (KNN Imputation)

Diberikan sebuah dataset campuran yang berisi informasi mahasiswa sebagai berikut:

| ID | Umur (Numerik) | IPK (Numerik) | Semester (Ordinal) | Jurusan (Kategorikal) |
| :---: | :---: | :---: | :---: | :--- |
| M1 | 20 | 3.50 | 4 | Informatika |
| M2 | 22 | 3.80 | 6 | Sistem Informasi |
| M3 | 21 | 3.20 | 4 | Informatika |
| **M4** | **23** | **3.60** | **8** | **?** *(Missing Value)* |
| M5 | 19 | 3.00 | 2 | Informatika |
| M6 | 24 | 3.90 | 8 | Sistem Informasi |
| M7 | 20 | 3.40 | 4 | Teknik Industri |
| M8 | 22 | 3.70 | 6 | Informatika |
| M9 | 21 | 3.10 | 2 | Sistem Informasi |
| M10 | 23 | 3.85 | 8 | Informatika |

Sebagai contoh kasus, diasumsikan mahasiswa dengan **ID M4** memiliki *missing value* pada atribut **Jurusan**. Karena target prediksinya (Jurusan) adalah data kategorikal, KNN tidak akan menggunakan nilai rata-rata, melainkan menggunakan nilai **Modus** (kelas yang paling sering muncul) dari tetangga terdekatnya.

## 1. Menghitung Jarak Euclidean

Sistem menghitung jarak antara data M4 dengan seluruh data mahasiswa lainnya menggunakan atribut numerik yang tersedia (`Umur`, `IPK`, dan `Semester`). 

Sebagai contoh, berikut adalah perhitungan jarak antara M4 dengan M10:

$$
\begin{aligned}
d(M4, M10) &= \sqrt{(23 - 23)^2 + (3.60 - 3.85)^2 + (8 - 8)^2} \\
d(M4, M10) &= \sqrt{0 + (-0.25)^2 + 0} \\
d(M4, M10) &= \sqrt{0.0625} \\
d(M4, M10) &= 0.250
\end{aligned}
$$

Dengan cara yang sama, jarak Euclidean dari M4 ke seluruh data lainnya dihitung untuk mencari data yang paling mirip.

## 2. Menentukan $k$ Tetangga Terdekat

Jika kita menetapkan parameter jumlah tetangga $k = 5$, sistem akan mengurutkan dan mengambil 5 data mahasiswa dengan jarak terkecil (terdekat) dari M4. Berdasarkan hasil perhitungan matematika, 5 tetangga terdekatnya adalah:

| Urutan | ID Data | Jarak Euclidean | Nilai `Jurusan` |
| :---: | :---: | :--- | :--- |
| 1 | M10 | 0.250 | Informatika |
| 2 | M6 | 1.044 | Sistem Informasi |
| 3 | M8 | 2.238 | Informatika |
| 4 | M2 | 2.245 | Sistem Informasi |
| 5 | M3 | 4.490 | Informatika |

## 3. Menghitung Nilai Prediksi (Modus)

Karena kolom `Jurusan` merupakan data kategorikal, nilai *missing value* pada M4 akan diisi menggunakan kelas mayoritas atau **Modus** dari kelima tetangga terdekat tersebut:

* **Informatika**: Muncul 3 kali (pada M10, M8, M3)
* **Sistem Informasi**: Muncul 2 kali (pada M6, M2)

**Hasil Prediksi:** Karena "Informatika" adalah jurusan yang paling dominan muncul di antara tetangga terdekatnya, maka *missing value* pada atribut Jurusan milik data **M4** diprediksi dan diisi dengan **Informatika**.