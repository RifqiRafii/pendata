# Analisis Klasifikasi Kesuburan Tanah

Analisis ini bertujuan untuk mengklasifikasikan kondisi tanah menjadi dua kelas, yaitu **Subur** dan **Tidak Subur**, berdasarkan 10 fitur agronomis. Algoritma dasar yang digunakan adalah *K-Nearest Neighbors* (KNN). 

![Keseluruhan Workflow KNIME](AKT1.png)

## 1. Pra-pemrosesan Data (Data Preprocessing)
Dataset awal memiliki beberapa ketidaksempurnaan, seperti adanya data yang hilang (*missing values*) dan perbedaan skala satuan antar fitur pengukuran. Langkah-langkah penanganannya adalah sebagai berikut:

### 1.1 Penanganan Nilai Hilang (Missing Values Imputation)
Fitur numerik diimputasi menggunakan **Median** dan fitur kategorikal diimputasi menggunakan **Modus** (Most Frequent Value).

![Konfigurasi Node Missing Value](AKT2.png)

### 1.2 Transformasi Data Kategorikal (One-Hot Encoding)
Fitur kategorikal `Tekstur Tanah` dikonversi menjadi representasi numerik biner menggunakan teknik *One-Hot Encoding*.

![Hasil Output One to Many](AKT3.png)

### 1.3 Standarisasi Skala (Z-Score Normalization)
Skala diseimbangkan menggunakan *Z-Score Standardization*, dengan rumus:

$$z = \frac{x - \mu}{\sigma}$$

## 2. Pemodelan K-Nearest Neighbors (KNN)
Pada analisis ini, nilai *hyperparameter* yang digunakan adalah **$k = 3$** untuk menghindari hasil seri saat pemungutan suara mayoritas pada klasifikasi biner. Jarak kedekatan dihitung menggunakan **Euclidean Distance**:

$$d(p, q) = \sqrt{\sum_{i=1}^{n} (q_i - p_i)^2}$$

![Konfigurasi K-Nearest Neighbor](AKT4.png)

## 3. Metrik Evaluasi
Kinerja model KNN ($k=3$) dievaluasi dengan membandingkan label hasil prediksi terhadap label aktual. 

![Confusion Matrix](AKT5.png)

Evaluasi kuantitatif dihitung menggunakan empat metrik berikut:
**1. Akurasi (Accuracy)**

$$\text{Accuracy} = \frac{TP + TN}{TP + TN + FP + FN}$$

**2. Presisi (Precision)**

$$\text{Precision} = \frac{TP}{TP + FP}$$

**3. Sensitivitas (Recall)**

$$\text{Recall} = \frac{TP}{TP + FN}$$

**4. F1-Score**

$$\text{F1-Score} = 2 \times \frac{\text{Precision} \times \text{Recall}}{\text{Precision} + \text{Recall}}$$

![Accuracy Statistics](AKT6.png)

## 4. Interpretasi Hasil dan Analisis Kritis

Berdasarkan pengujian pada 400 sampel data uji (20% dari dataset), model KNN ($k=3$) menghasilkan performa yang sangat baik dan realistis. Model mencapai **Akurasi sebesar 92%**, yang berarti model berhasil mengklasifikasikan 368 dari 400 sampel dengan benar.

Dari *Confusion Matrix*, kita dapat mengamati bahwa model menghasilkan 13 *False Positives* (tanah Tidak Subur yang diprediksi Subur) dan 19 *False Negatives* (tanah Subur yang diprediksi Tidak Subur). Hal ini menghasilkan nilai **Presisi 93.2%** dan **Recall 90.5%**. 

Secara agronomis, nilai Presisi yang sedikit lebih tinggi daripada Recall ini menguntungkan. Artinya, model lebih berhati-hati dalam melabeli suatu tanah sebagai "Subur". Ini mengurangi risiko kerugian petani akibat menanam atau memberikan *treatment* lahan pada tanah yang sebenarnya tidak potensial (menekan angka *False Positive*). Kesalahan sebanyak 8% secara keseluruhan sangat wajar dan menunjukkan bahwa model memiliki kemampuan generalisasi yang baik (*good fit*) tanpa mengalami indikasi *overfitting*.