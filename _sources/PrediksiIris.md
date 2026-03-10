# Prediksi Missing Value pada Data ke-40 (KNN Imputation)

Sebagai contoh kasus, diasumsikan pada data ke-40 (indeks ke-39) di dataset IRIS terdapat *missing value* pada kolom `sepal_length`. Nilai atribut lain yang diketahui pada data tersebut adalah:
* `sepal_width`: 3.4
* `petal_length`: 1.5
* `petal_width`: 0.2

## 1. Konsep Perhitungan KNN

**a. Menghitung Jarak**
Sistem menghitung jarak antara data ke-40 dengan seluruh data lainnya menggunakan atribut yang tersedia (`sepal_width`, `petal_length`, dan `petal_width`). Jarak dihitung menggunakan rumus **Euclidean Distance**:

$$d(x, y) = \sqrt{\sum_{i=1}^{n}(x_i - y_i)^2}$$

**b. Menentukan $k$ Tetangga Terdekat**
Dengan menggunakan parameter $k = 5$, sistem mencari 5 data dengan jarak terdekat (paling mirip) dengan data ke-40. Hasil pencarian tetangga terdekat adalah:

| Urutan | Data ke- | Jarak Euclidean | Nilai `sepal_length` |
| :--- | :--- | :--- | :--- |
| 1 | 8 | 0.000 | 5.0 |
| 2 | 12 | 0.100 | 4.8 |
| 3 | 28 | 0.100 | 5.2 |
| 4 | 29 | 0.100 | 5.2 |
| 5 | 7 | 0.141 | 4.6 |

**c. Menghitung Nilai Prediksi**
Karena atribut `sepal_length` bertipe numerik, nilai *missing value* diisi menggunakan rata-rata (*mean*) dari kelima tetangga terdekat tersebut:

$$
\begin{aligned}
\hat{x} &= \frac{5.0 + 4.8 + 5.2 + 5.2 + 4.6}{5} \\
\hat{x} &= \frac{24.8}{5} \\
\hat{x} &= 4.96
\end{aligned}
$$

**Hasil:** Nilai `sepal_length` pada data ke-40 diprediksi sebesar **4.96** (mendekati nilai aslinya yaitu 5.1).

---

## 2. Implementasi di Orange Data Mining

Untuk melakukan prediksi di atas menggunakan software **Orange Data Mining**, ikuti langkah-langkah berikut:

**Langkah 1: Import Dataset**
Gunakan widget `File` untuk memuat dataset IRIS yang telah dikondisikan memiliki *missing value* (kosong atau ditandai `?`) pada baris ke-40 kolom `sepal_length`.

**Langkah 2: Cek Data Awal**
Hubungkan widget `File` ke `Data Table` pertama untuk memastikan baris ke-40 terbaca sebagai *missing value*.

**Langkah 3: Konfigurasi Widget Impute**
1. Tambahkan widget `Impute` dan hubungkan dengan output dari widget `File`.
2. Buka pengaturan `Impute`.
3. Pada pilihan metode, pilih **k-Nearest Neighbors (kNN)**.
4. Atur parameter *Number of neighbors* menjadi **5**.
![alt text](impute.png)
**Langkah 4: Evaluasi Hasil**
Tambahkan widget `Data Table` kedua dan hubungkan dari output widget `Impute`. Buka tabel tersebut dan periksa baris ke-40; kolom `sepal_length` kini telah terisi dengan nilai hasil prediksi KNN.
![alt text](hasilKKNiris.png)