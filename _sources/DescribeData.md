# Describing Data
Describe Data atau mendeskripsikan data merupakan salah satu tahapan penting dalam suatu penelitian untuk menyajikan data yang telah diperoleh agar lebih mudah dipahami oleh pembaca. Berbagai metode dapat digunakan untuk mengolah dan menampilkan hasil data, seperti menghitung nilai rata-rata, menentukan nilai yang paling sering muncul, serta ukuran statistik lainnya.

## Statistik Deskriptif (Describe Data)

Statistik deskriptif digunakan untuk menggambarkan karakteristik utama dataset melalui ukuran pemusatan dan penyebaran data. Pada dataset IRIS, analisis dilakukan terhadap empat atribut numerik:

- Sepal Length  
- Sepal Width  
- Petal Length  
- Petal Width  

---

## Ukuran Statistik yang Digunakan

Ukuran statistik yang dianalisis meliputi:

- **Minimum (Min)** → Nilai terkecil
- **Maximum (Max)** → Nilai terbesar
- **Mean** → Nilai rata-rata
- **Median** → Nilai tengah
- **Standard Deviation (Std)** → Tingkat penyebaran data

---

## Hasil Statistik Deskriptif Dataset IRIS

Berikut ringkasan statistik deskriptif dataset IRIS:

| Atribut        | Min  | Max  | Mean | Std Dev |
|---------------|------|------|------|--------|
| Sepal Length  | 4.3  | 7.9  | 5.84 | 0.83   |
| Sepal Width   | 2.0  | 4.4  | 3.05 | 0.43   |
| Petal Length  | 1.0  | 6.9  | 3.76 | 1.76   |
| Petal Width   | 0.1  | 2.5  | 1.20 | 0.76   |

![alt text](statistik.png)

## Mengukur Jarak (Distance Measurement)
Selain menggunakan statistik deskriptif, analisis data juga dapat dilakukan dengan mengukur jarak (distance) antar data. Pengukuran jarak bertujuan untuk mengetahui tingkat kemiripan (similarity) atau perbedaan (dissimilarity) antar objek dalam dataset.

Pada dataset IRIS, setiap data bunga direpresentasikan oleh empat atribut numerik:
-Sepal Length
-Sepal Width
-Petal Length
-Petal Width

Karena seluruh atribut bersifat numerik, metode jarak yang umum digunakan adalah:
- Euclidean Distance
- Manhattan Distance
- Cosine Distance

## Mengukur Distance Menggunakan Orange

Untuk menghitung jarak antar data pada dataset IRIS, digunakan software **Orange Data Mining**. Proses ini bertujuan untuk mengetahui tingkat kemiripan atau perbedaan antar objek berdasarkan atribut numerik.

### Langkah-langkah

1. Buka aplikasi **Orange**  
2. Tambahkan widget **File**  
3. Pilih dataset bawaan **iris**  
4. Tambahkan widget **Distances**  
5. Hubungkan `File → Distances`  
6. Pilih metode jarak (Euclidean, Manhattan, atau Cosine)  
7. Hubungkan ke: Distance Map

---

## Konsep Perhitungan Distance

### Euclidean Distance

$$
d(x,y) = \sqrt{\sum_{i=1}^{n} (x_i - y_i)^2}
$$

### Manhattan Distance

$$
d(x,y) = \sum_{i=1}^{n} |x_i - y_i|
$$

### Cosine Similarity

$$
\text{cosine similarity} =
\frac{x \cdot y}{||x|| \, ||y||}
$$

### Cosine Distance

$$
\text{cosine distance} = 1 - \text{cosine similarity}
$$

---

## Output Distance Matrix

Widget **Distances** menghasilkan matriks jarak (distance matrix) yang berisi jarak antar setiap pasangan data.

Karakteristik matriks jarak:

- Nilai kecil → data semakin mirip  
- Nilai besar → data semakin berbeda  
- Elemen diagonal selalu bernilai:

$$
d(x,x) = 0
$$

karena jarak suatu data terhadap dirinya sendiri adalah nol.

---

# Interpretasi Hasil Distance

## Kemiripan Dalam Spesies

Data dengan spesies yang sama (misalnya *setosa*) memiliki nilai jarak yang relatif kecil. Hal ini menunjukkan bahwa karakteristik bunga dalam satu spesies cenderung homogen.

## Perbedaan Antar Spesies

Data dari spesies berbeda (misalnya *setosa* dan *virginica*) memiliki nilai jarak yang lebih besar.

Atribut yang paling berkontribusi terhadap perbedaan tersebut adalah:

- Petal Length  
- Petal Width  

Karena kedua atribut ini memiliki variasi nilai yang signifikan antar spesies.

## Visualisasi Distance Map

Pada widget **Distance Map**:
![alt text](DistanceMap.png)
- Warna lebih gelap → jarak kecil (lebih mirip)  
- Warna lebih terang → jarak besar (lebih berbeda)  

Biasanya terlihat blok gelap di sepanjang diagonal matriks yang menunjukkan adanya pengelompokan alami berdasarkan spesies.

# Pengukuran Jarak pada Data Campuran

Pada bagian ini dibuat contoh data campuran yang terdiri dari atribut numerik, ordinal, dan kategorikal. Metode yang digunakan adalah **Gower Distance** karena mampu menangani berbagai tipe atribut dalam satu perhitungan.

---

# Contoh Data Campuran

| ID  | Umur (Numerik) | IPK (Numerik) | Semester (Ordinal) | Jurusan (Kategorikal) |
|-----|---------------|---------------|--------------------|------------------------|
| M1  | 20            | 3.50          | 4                  | Informatika           |
| M2  | 22            | 3.80          | 6                  | Sistem Informasi      |
| M3  | 21            | 3.20          | 4                  | Informatika           |
| M4  | 23            | 3.60          | 8                  | Teknik Industri       |
| M5  | 19            | 3.00          | 2                  | Informatika           |
| M6  | 24            | 3.90          | 8                  | Sistem Informasi      |
| M7  | 20            | 3.40          | 4                  | Teknik Industri       |
| M8  | 22            | 3.70          | 6                  | Informatika           |
| M9  | 21            | 3.10          | 2                  | Sistem Informasi      |
| M10 | 23            | 3.85          | 8                  | Informatika           |

---

# Metode Pengukuran: Gower Distance

Rumus umum Gower Distance:

$$
D_{ij} = \frac{\sum_{k=1}^{p} s_{ijk}}{p}
$$

Keterangan:

- $D_{ij}$ = jarak antara objek $i$ dan $j$
- $p$ = jumlah atribut
- $s_{ijk}$ = skor jarak atribut ke-$k$

---

# Perhitungan Setiap Tipe Atribut

## 1. Atribut Numerik

Untuk atribut numerik digunakan normalisasi:

$$
s_{ijk} = \frac{|x_{ik} - x_{jk}|}{\text{range}_k}
$$

Range dihitung dengan:

$$
\text{range}_k = \text{max}_k - \text{min}_k
$$

---

## 2. Atribut Ordinal

Karena semester bersifat berurutan (2, 4, 6, 8), maka dihitung seperti numerik setelah dinormalisasi.

---

## 3. Atribut Kategorikal

Untuk atribut kategorikal:

$$
s_{ijk} =
\begin{cases}
0, & \text{jika kategori sama} \\
1, & \text{jika kategori berbeda}
\end{cases}
$$

---

# Contoh Perhitungan Jarak M1 dan M2

## Langkah 1: Hitung Range

Umur:

$$
24 - 19 = 5
$$

IPK:

$$
3.90 - 3.00 = 0.90
$$

Semester:

$$
8 - 2 = 6
$$

---

## Langkah 2: Hitung Selisih Ternormalisasi

Umur:

$$
\frac{|20 - 22|}{5} = 0.40
$$

IPK:

$$
\frac{|3.50 - 3.80|}{0.90} = 0.33
$$

Semester:

$$
\frac{|4 - 6|}{6} = 0.33
$$

Jurusan (Informatika vs Sistem Informasi):

$$
s = 1
$$

---

## Langkah 3: Hitung Gower Distance

$$
D_{M1,M2} = \frac{0.40 + 0.33 + 0.33 + 1}{4}
$$

$$
D_{M1,M2} = 0.515
$$

---

# Interpretasi Hasil

Rentang nilai Gower Distance:

$$
0 \leq D_{ij} \leq 1
$$

- Nilai mendekati 0 → objek sangat mirip  
- Nilai mendekati 1 → objek sangat berbeda  

Nilai 0.515 menunjukkan bahwa M1 dan M2 memiliki tingkat perbedaan sedang.

---

# Kesimpulan

Gower Distance efektif digunakan pada data campuran karena mampu menggabungkan atribut numerik, ordinal, dan kategorikal dalam satu ukuran jarak terintegrasi.