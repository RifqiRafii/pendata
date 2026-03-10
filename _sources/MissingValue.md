# Missing Value

## Missing Value dan Prediksi Missing Value dengan KNN

### Missing Value
Missing value adalah kondisi ketika suatu data pada dataset tidak memiliki nilai atau bernilai kosong. Hal ini sering terjadi dalam proses pengumpulan data karena berbagai faktor seperti kesalahan pencatatan, data yang tidak lengkap dari responden, kerusakan data, atau proses penggabungan dataset yang tidak sempurna.

Keberadaan missing value dapat mempengaruhi kualitas analisis data karena beberapa algoritma tidak dapat memproses data yang memiliki nilai kosong. Oleh karena itu, missing value perlu ditangani sebelum dilakukan proses analisis atau pemodelan data.

Beberapa metode yang umum digunakan untuk menangani missing value antara lain:

- **Menghapus data (Deletion)**
    Baris atau kolom yang memiliki nilai kosong dihapus dari dataset.
- **Imputasi menggunakan nilai statistik**
    Nilai kosong diganti dengan nilai rata-rata (mean), median, atau modus.
- **Interpolasi**
    Digunakan pada data runtun waktu untuk memperkirakan nilai yang hilang.
- **Metode berbasis Machine Learning**
    Seperti K-Nearest Neighbor (KNN) untuk memprediksi nilai yang hilang berdasarkan kemiripan data.
    
## Prediksi Missing Value dengan K-Nearest Neighbor (KNN)

Salah satu metode yang dapat digunakan untuk mengisi missing value adalah K-Nearest Neighbor (KNN). Metode ini bekerja dengan mencari sejumlah data yang paling mirip dengan data yang memiliki nilai kosong, kemudian memperkirakan nilai tersebut berdasarkan tetangga terdekatnya.

Prinsip dasar metode KNN adalah bahwa objek yang memiliki karakteristik serupa cenderung memiliki nilai yang mirip.

Langkah-langkah dalam melakukan imputasi missing value menggunakan KNN adalah sebagai berikut:
1. Menentukan nilai parameter 𝑘 yang menunjukkan jumlah tetangga terdekat yang akan digunakan.
2. Menghitung jarak antara data yang memiliki missing value dengan data lainnya.
3. Menentukan 𝑘 data terdekat berdasarkan nilai jarak.
4. Mengisi missing value menggunakan rata-rata (untuk data numerik) atau modus (untuk data kategorikal) dari tetangga terdekat tersebut.

$$d(x, y) = \sqrt{\sum_{i=1}^{n}(x_i - y_i)^2}$$

dimana:
𝑥 = data yang memiliki missing value
𝑦 = data pembanding
𝑛 = jumlah atribut

**Contoh Sederhana**
| Data | Umur | Penghasilan |
| ---- | ---- | ----------- |
| A    | 20   | 2.5         |
| B    | 21   | 2.7         |
| C    | 22   | ?           |
| D    | 23   | 3.0         |

Nilai penghasilan pada data C tidak diketahui sehingga perlu diprediksi menggunakan metode KNN.

Misalkan digunakan:
$$𝑘 = 2 $$ artinya kita akan menggunakan 2 tetangga terdekat.

### Langkah 1: Menghitung Jarak

Karena atribut yang tersedia adalah **Umur**, maka jarak dapat dihitung menggunakan **Euclidean Distance**.

Rumus Euclidean Distance:

$$d(x, y) = \sqrt{(x - y)^2}$$

---

**Jarak antara C dan A**

$$
\begin{aligned}
d(C, A) &= \sqrt{(22 - 20)^2} \\
d(C, A) &= \sqrt{4} \\
d(C, A) &= 2
\end{aligned}
$$

---

**Jarak antara C dan B**

$$
\begin{aligned}
d(C, B) &= \sqrt{(22 - 21)^2} \\
d(C, B) &= \sqrt{1} \\
d(C, B) &= 1
\end{aligned}
$$

**Jarak antara C dan D**

$$
\begin{aligned}
d(C, D) &= \sqrt{(22 - 23)^2} \\
d(C, D) &= \sqrt{1} \\
d(C, D) &= 1
\end{aligned}
$$

---

**Jarak antara C dan E**

$$
\begin{aligned}
d(C, E) &= \sqrt{(22 - 24)^2} \\
d(C, E) &= \sqrt{4} \\
d(C, E) &= 2
\end{aligned}
$$

---

### Langkah 2: Menentukan Tetangga Terdekat

Urutan jarak terkecil:

| Data | Jarak |
| :--- | :--- |
| B | 1 |
| D | 1 |
| A | 2 |
| E | 2 |

Karena:

$$k = 2$$

maka dua tetangga terdekat adalah:
* B
* D

---

### Hasil Prediksi

Nilai penghasilan yang hilang pada data **C** diperkirakan sebesar:

$$2.85$$

Sehingga dataset menjadi:

| Data | Umur | Penghasilan |
| :--- | :--- | :--- |
| A | 20 | 2.5 |
| B | 21 | 2.7 |
| C | 22 | 2.85 |
| D | 23 | 3.0 |
| E | 24 | 3.2 |

### Kesimpulan
Missing value merupakan permasalahan umum dalam pengolahan data yang dapat mempengaruhi hasil analisis. Salah satu metode yang efektif untuk mengatasi missing value adalah metode K-Nearest Neighbor (KNN), yang memanfaatkan kemiripan antar data untuk memperkirakan nilai yang hilang. Dengan pendekatan ini, nilai yang diprediksi menjadi lebih representatif karena didasarkan pada pola data yang ada.

---

## Prediksi Missing Value Menggunakan KNN di Orange

Pada aplikasi **Orange Data Mining**, missing value dapat diprediksi menggunakan metode **KNN Imputation** melalui widget **Impute**.

### Langkah-langkah di Orange

**1 Import Dataset**

Gunakan widget:

`File`

untuk memasukkan dataset yang memiliki missing value.

---

**2 Tampilkan Data**

Hubungkan ke widget:

`Data Table`

untuk melihat apakah terdapat nilai yang hilang (biasanya ditandai dengan tanda ?).

---

**3 Gunakan Widget Impute**

Tambahkan widget:

`Impute`

Widget ini digunakan untuk mengisi missing value.

---

**4 Pilih Metode KNN**

Pada pengaturan **Impute**, pilih metode:

`KNN Imputation`

Parameter yang dapat diatur:
* **Number of Neighbors (k)** → jumlah tetangga yang digunakan
* **Distance Measure** → metode perhitungan jarak
* **Weighting** → bobot berdasarkan jarak

---

**5 Hubungkan ke Data Table**

Hubungkan output **Impute** → **Data Table** untuk melihat hasil data setelah missing value diisi.

---

### Cara Kerja KNN Imputation di Orange

Proses yang dilakukan Orange adalah:
1. Mendeteksi data yang memiliki missing value.
2. Menghitung jarak antara data tersebut dengan data lainnya.
3. Menentukan **k tetangga terdekat**.
4. Mengisi nilai yang hilang menggunakan:
   * **rata-rata** (mean) untuk data numerik
   * **modus** untuk data kategorikal.

---

### Kelebihan Metode KNN

* Tidak menghapus data yang memiliki missing value.
* Memanfaatkan pola kemiripan antar data.
* Lebih akurat dibanding imputasi sederhana seperti mean.

---

### Kekurangan Metode KNN

* Membutuhkan waktu komputasi lebih besar pada dataset besar.
* Sensitif terhadap pemilihan nilai k.
* Hasil dapat dipengaruhi oleh outlier.