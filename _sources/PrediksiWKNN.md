---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# Prediksi Missing Value dengan WKNN

Selain algoritma KNN standar, terdapat varian yang lebih tingkat lanjut yaitu **WKNN (Weighted K-Nearest Neighbors)**. WKNN memberikan "bobot" (*weight*) pada setiap tetangga berdasarkan jaraknya. Tetangga yang jaraknya lebih dekat akan memiliki pengaruh (bobot) yang lebih besar terhadap hasil prediksi dibandingkan tetangga yang letaknya lebih jauh.

## Dataset

Diberikan sebuah dataset uji coba dengan tiga variabel independen ($X_1, X_2, X_3$) dan satu variabel dependen ($Y$). Terdapat *missing value* pada data **T3**.

| Data | X1 | X2 | X3 | Y |
| :---: | :---: | :---: | :---: | :---: |
| T1 | 3 | 5 | 4 | 10 |
| T2 | 2 | 1 | 2 | 5 |
| **T3** | **5** | **4** | **6** | **?** |
| T4 | 1 | 2 | 3 | 6 |
| T5 | 4 | 6 | 5 | 12 |

---

## Perhitungan Manual WKNN

Untuk mendemonstrasikan cara kerja WKNN secara matematis dengan lebih mudah dipahami, kita akan melakukan perhitungan manual **tanpa normalisasi** menggunakan parameter $k = 3$.

### 1. Menghitung Jarak Euclidean

Sistem menghitung jarak antara target **T3** dengan seluruh data lainnya menggunakan rumus Euclidean:

$$d(x, y) = \sqrt{\sum_{i=1}^{n}(x_i - y_i)^2}$$

* **Jarak ke T1:**
$$d(T3, T1) = \sqrt{(5-3)^2 + (4-5)^2 + (6-4)^2} = \sqrt{4 + 1 + 4} = \sqrt{9} = 3$$

* **Jarak ke T2:**
$$d(T3, T2) = \sqrt{(5-2)^2 + (4-1)^2 + (6-2)^2} = \sqrt{9 + 9 + 16} = \sqrt{34} \approx 5.83$$

* **Jarak ke T4:**
$$d(T3, T4) = \sqrt{(5-1)^2 + (4-2)^2 + (6-3)^2} = \sqrt{16 + 4 + 9} = \sqrt{29} \approx 5.385$$

* **Jarak ke T5:**
$$d(T3, T5) = \sqrt{(5-4)^2 + (4-6)^2 + (6-5)^2} = \sqrt{1 + 4 + 1} = \sqrt{6} \approx 2.45$$

Karena kita menggunakan parameter $k = 3$, kita ambil 3 data dengan jarak terdekat, yaitu: **T5 (2.45)**, **T1 (3)**, dan **T4 (5.385)**.

### 2. Menghitung Bobot (Weight)

Berbeda dengan KNN yang langsung merata-rata, WKNN menghitung bobot ($w$) masing-masing tetangga. Rumus bobot yang paling umum digunakan adalah kebalikan dari jarak:

$$w_i = \frac{1}{d_i}$$

Maka bobot untuk 3 tetangga terdekat:
* $w_{T5} = \frac{1}{2.45} \approx 0.408$
* $w_{T1} = \frac{1}{3} \approx 0.333$
* $w_{T4} = \frac{1}{5.385} \approx 0.185$

### 3. Menghitung Nilai Prediksi

Nilai prediksi ($\hat{Y}$) pada data numerik dihitung dengan mengalikan bobot masing-masing tetangga dengan nilai targetnya, lalu dibagi dengan total keseluruhan bobot:

$$\hat{Y} = \frac{\sum_{i=1}^{k} w_i \cdot Y_i}{\sum_{i=1}^{k} w_i}$$

$$
\begin{aligned}
\hat{Y} &= \frac{(0.408 \times 12) + (0.333 \times 10) + (0.185 \times 6)}{0.408 + 0.333 + 0.185} \\
\hat{Y} &= \frac{4.896 + 3.33 + 1.11}{0.926} \\
\hat{Y} &= \frac{9.336}{0.926} \approx 10.08
\end{aligned}
$$

Jadi, hasil prediksi *missing value* untuk T3 secara manual adalah **10.08**.

---

## Implementasi dengan Python (Sklearn)

Dalam praktik *data mining* yang sesungguhnya, **data wajib dinormalisasi terlebih dahulu** agar variabel dengan skala besar tidak mendominasi perhitungan jarak. 

Berikut adalah kode Python menggunakan library `scikit-learn` untuk melakukan **Normalisasi** (Min-Max) dan memprediksi nilai menggunakan **WKNN**:

```{code-cell} python
import pandas as pd
import numpy as np
from sklearn.preprocessing import MinMaxScaler
from sklearn.neighbors import KNeighborsRegressor

# 1. Membuat Dataset
data = {
    'T': ['T1', 'T2', 'T3', 'T4', 'T5'],
    'X1': [3, 2, 5, 1, 4],
    'X2': [5, 1, 4, 2, 6],
    'X3': [4, 2, 6, 3, 5],
    'Y': [10, 5, np.nan, 6, 12] # np.nan mewakili missing value
}
df = pd.DataFrame(data)

# Memisahkan data training (ada nilai Y) dan data testing (missing Y)
df_train = df.dropna(subset=['Y'])
df_missing = df[df['Y'].isnull()]

# Menentukan kolom fitur
features = ['X1', 'X2', 'X3']

# 2. PROSES NORMALISASI (Min-Max Scaler)
scaler = MinMaxScaler()

# Fit & transform pada data latih, lalu transform pada data missing
X_train_norm = scaler.fit_transform(df_train[features])
X_missing_norm = scaler.transform(df_missing[features])
y_train = df_train['Y'].values

# 3. PEMODELAN WKNN
# Menggunakan k=3 dan weights='distance' untuk menerapkan WKNN
wknn = KNeighborsRegressor(n_neighbors=3, weights='distance')
wknn.fit(X_train_norm, y_train)

# 4. PREDIKSI
y_pred = wknn.predict(X_missing_norm)

print(f"Hasil prediksi Y untuk T3 dengan WKNN: {y_pred[0]:.2f}")