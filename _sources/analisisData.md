# Analisis Keterjelasan Model (*Explainability*) pada Forecasting Deret Waktu

Berdasarkan dokumen *Jupyter Notebook* yang dikaji, berikut adalah analisis mendalam mengenai pemodelan dan proses interpretasi *machine learning* yang dilakukan.

---

## 1. Analisa Prediksi Tentang Apa?

Analisis dalam *notebook* tersebut berfokus pada penerapan teknik **Keterjelasan Model Machine Learning (Explainability / Interpretability)** pada kasus **Peramalan Deret Waktu (*Time Series Forecasting*)**. 

Secara spesifik, model dibangun untuk memprediksi **Total Permintaan Listrik (*Electricity Demand*)** di wilayah Victoria, Australia (`vic_electricity` dataset). Alih-alih hanya berfokus pada seberapa akurat metrik error yang dihasilkan, tujuan utama dari *notebook* ini adalah untuk mendemistifikasi (*unboxing*) algoritma yang bertindak sebagai *black-box* (dalam hal ini, **LightGBM Regressor**). 

Fokus prediksinya adalah menjawab pertanyaan analitik tingkat lanjut:
* Mengapa model menghasilkan angka prediksi tertentu?
* Fitur atau variabel apa yang paling memengaruhi model secara keseluruhan (*Global Interpretability*)?
* Bagaimana kontribusi spesifik dari setiap fitur pada satu hari prediksi tertentu (*Local Interpretability*)?

---

## 2. Bagaimana Bentuk Data Training-nya?

Sebelum dimasukkan ke dalam model, data mentah beresolusi tinggi (per 30 menit) telah melalui proses **Agregasi Waktu (*Resampling*)** menjadi frekuensi harian (*Daily frequency*). Data dibagi secara temporal (*Time-based split*) di mana data latih (*Train*) menggunakan rentang hingga **21 Desember 2014**, dan data uji (*Test*) dimulai dari **22 Desember 2014**.

Berikut adalah rincian bentuk input dan output dari data pelatihannya:

| Komponen Data | Nama Variabel | Deskripsi / Sifat Variabel |
| :--- | :--- | :--- |
| **Output (Target / Y)** | `Demand` | Total penjumlahan (*sum*) konsumsi listrik pada suatu hari tertentu. Ini adalah variabel dependen yang ingin ditebak oleh model. |
| **Input (Fitur Internal / X)** | `lag_1` hingga `lag_7` | Nilai permintaan listrik dari 7 hari sebelumnya. Ini adalah variabel independen turunan (*autoregressive features*) yang di-*generate* oleh *library* `skforecast`. |
| **Input (Fitur Eksternal / X)** | `Temperature` | Nilai rata-rata suhu harian (*mean*). Ini bertindak sebagai variabel eksogen (faktor dari luar sistem deret waktu) yang terbukti memengaruhi perilaku konsumsi listrik. |

---

## 3. Apa Itu Lag?

Dalam konteks analisis deret waktu (*time series*), **Lag (Jeda/Kelambatan)** merujuk pada nilai observasi dari variabel target di masa lalu (periode sebelumnya) yang digunakan sebagai variabel prediktor (input) untuk meramalkan nilai di masa depan. 

Algoritma *machine learning* standar (seperti *Decision Tree*, *Random Forest*, atau *LightGBM*) tidak memiliki konsep urutan waktu secara bawaan. Oleh karena itu, kita harus "menggeser" data historis menjadi kolom-kolom fitur agar algoritma bisa belajar.

**Contoh Implementasi pada Kasus Ini:**
Jika model ingin memprediksi permintaan listrik untuk **Hari Ini (Waktu t)**, maka:
* `lag_1` = Permintaan listrik 1 hari yang lalu (Kemarin).
* `lag_2` = Permintaan listrik 2 hari yang lalu.
* `lag_7` = Permintaan listrik 7 hari yang lalu (Tepat seminggu yang lalu).

**Fungsi Utama Lag:** Penggunaan 7 level *lag* sangat krusial karena model membutuhkan data tersebut untuk menangkap **Pola Autokorelasi** dan **Siklus Musiman (Seasonality)**. Misalnya, tren penggunaan listrik di hari Minggu biasanya sangat mirip dengan hari Minggu di minggu sebelumnya (`lag_7`).

---

## 4. Jelaskan Proses Analisis yang Dilakukan

Proses analisis dalam studi kasus ini dilakukan secara runut, mulai dari pembentukan model hingga pembedahan logika model menggunakan 4 metode interpretasi yang berbeda. Berikut adalah alur analisis lengkapnya:

### A. Persiapan Data dan Pemodelan (Modeling)
1. **Data Preprocessing:** Dataset `vic_electricity` di-*resample* menjadi data harian (`sum` untuk *Demand* dan `mean` untuk *Temperature*).
2. **Train-Test Split:** Data dipisah secara sekuensial (bukan acak) untuk menjaga urutan waktu.
3. **Training Model:** Membangun *forecaster* rekursif (`ForecasterRecursive`) menggunakan algoritma regresi *gradient boosting* (`LGBMRegressor`). Model dilatih (`fit`) menggunakan 7 lags dan 1 variabel eksogen.

### B. Analisis Keterjelasan Model (*Explainability Analysis*)
Setelah model dilatih, analisis difokuskan pada interpretasi menggunakan 4 teknik berikut:

| Tahap Analisis | Metode yang Digunakan | Penjelasan Proses & Tujuan |
| :--- | :--- | :--- |
| **1. Fitur Bawaan Model** | *Model-Specific Feature Importances* | Mengekstrak nilai `feature_importances_` bawaan dari model LightGBM. Ini adalah analisis dasar untuk melihat skor bobot setiap fitur secara cepat berdasarkan seberapa sering fitur tersebut membelah cabang (*Decision Tree split*). |
| **2. Analisis SHAP (Global)** | *SHAP Summary Plot* & *Dependence Plot* | Menggunakan *SHapley Additive exPlanations* berbasis Teori Permainan (*Game Theory*). Proses ini merata-ratakan nilai SHAP pada data *training* untuk melihat sejauh mana sebuah fitur mendorong prediksi menjauh dari nilai dasar (rata-rata). *Dependence plot* digunakan untuk melihat korelasi arah (positif/negatif) antara nilai suhu tinggi/rendah terhadap output model. |
| **3. Analisis SHAP (Lokal)** | *Waterfall Plot* & *Force Plot* | Model melakukan prediksi untuk 10 hari ke depan, lalu satu tanggal spesifik diisolasi (misal: 28 Desember 2014). Proses ini secara presisi menghitung persentase kontribusi setiap fitur pada hari itu. Analisis menemukan bahwa prediksi nilai yang lebih rendah dari biasanya disebabkan oleh tarikan negatif yang kuat dari nilai `lag_1`. |
| **4. Uji Ketangguhan Fitur** | *Permutation Feature Importance* | Menganalisis fitur dengan cara eksperimental: mengacak urutan nilai pada satu kolom secara acak (memutus hubungan dengan variabel target) dan melihat seberapa jauh performa/akurasi model menjadi anjlok. Jika *error* meningkat drastis saat kolom `Temperature` diacak, artinya model sangat bergantung pada fitur tersebut. |
| **5. Analisis Efek Marjinal** | *Partial Dependence Plots (PDP)* | Memvisualisasikan efek dari nilai suatu fitur terhadap prediksi, dengan mengabaikan (merata-ratakan) efek dari semua fitur lainnya. Proses ini memastikan kita bisa melihat tren murni dari satu variabel, misalnya melihat pada titik suhu berapa derajat konsumsi listrik akan mulai melonjak secara drastis. |

**Kesimpulan Proses:** Analisis tidak berhenti pada sekadar seberapa akurat tebakan akhir model, melainkan membedah kontribusi kuantitatif dari nilai historis (`lags`) dan lingkungan (`Temperature`) pada setiap keputusan algoritma di balik layar.