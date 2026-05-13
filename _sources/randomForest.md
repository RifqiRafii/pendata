# Klasifikasi Lanjut: Algoritma Random Forest

**Random Forest** adalah salah satu algoritma *Machine Learning* paling tangguh yang termasuk dalam kategori **Ensemble Learning**. Prinsip dasar dari *Ensemble Learning* adalah menggabungkan prediksi dari beberapa model individu untuk menghasilkan satu keputusan akhir yang lebih akurat, stabil, dan tahan terhadap anomali data dibandingkan jika hanya menggunakan satu model tunggal.

Jika *Decision Tree* dianalogikan sebagai satu pohon keputusan, maka *Random Forest* adalah sebuah "hutan" yang terdiri dari ratusan hingga ribuan pohon keputusan yang bekerja secara kolektif.



## 1. Mekanisme Kerja: Bagaimana "Hutan" Terbentuk?

Random Forest tidak hanya sekadar membuat banyak pohon, tetapi memastikan setiap pohon di dalamnya memiliki karakteristik yang unik melalui dua teknik utama:

### A. Bagging (Bootstrap Aggregating)
Algoritma mengambil sampel data dari dataset asli secara acak dengan pengembalian (*with replacement*). Proses ini disebut **Bootstrapping**. Akibatnya, setiap pohon di dalam hutan akan dilatih menggunakan subset data yang berbeda-beda. Ada data yang mungkin muncul berulang kali di satu pohon, namun tidak muncul sama sekali di pohon lainnya.

### B. Feature Randomness (Keacakan Fitur)
Dalam *Decision Tree* standar, setiap node dievaluasi menggunakan seluruh fitur yang tersedia untuk mencari pembagi terbaik. Namun, pada *Random Forest*, saat sebuah node akan dibelah, algoritma hanya memilih sebagian kecil fitur secara acak dari total fitur yang ada. Keacakan ini memastikan bahwa pohon-pohon di dalam hutan tidak identik, sehingga meningkatkan keragaman model.

### C. Majority Voting (Pemungutan Suara)
Setelah semua pohon selesai dilatih, proses prediksi dilakukan dengan cara:
1.  Setiap pohon memberikan satu "suara" (prediksi) untuk data baru yang diuji.
2.  Random Forest menghitung total suara dari seluruh pohon di dalam hutan.
3.  Hasil akhir ditentukan berdasarkan suara terbanyak (untuk klasifikasi) atau nilai rata-rata (untuk regresi).

---

## 2. Implementasi Detail di KNIME Analytics Platform

Berdasarkan standar implementasi pada *workflow* KNIME, berikut adalah rincian konfigurasi node utama yang digunakan:

### A. Random Forest Learner
Node ini bertanggung jawab untuk membangun "hutan" berdasarkan data latih.
* **Target Column**: Menentukan kolom kelas yang ingin diprediksi (misalnya: `Play Tennis`).
* **Number of Models**: Menentukan jumlah pohon yang akan dibangun. Angka standar biasanya dimulai dari 100 pohon; semakin banyak pohon, model akan semakin stabil namun memakan waktu komputasi lebih lama.
* **Attribute Selection**: Mengatur jumlah fitur acak yang dipertimbangkan di setiap pemisahan node. Umumnya menggunakan akar kuadrat dari total jumlah fitur ($\sqrt{n}$).
* **Tree Settings**: Pengguna dapat mengatur kedalaman maksimal pohon atau menggunakan kriteria kualitas seperti *Information Gain* atau *Gain Ratio*.

### B. Random Forest Predictor
Node ini berfungsi untuk mengaplikasikan model hutan yang sudah dilatih ke data uji.
* **Input Ports**: Memiliki dua input; port atas untuk model (dari *Learner*) dan port bawah untuk data uji (dari *Partitioning*).
* **Output Data**: Berbeda dengan *Decision Tree* biasa, *Predictor* pada Random Forest menghasilkan kolom prediksi akhir beserta kolom **probabilitas** untuk setiap kelas berdasarkan persentase suara dari seluruh pohon.

---

## 3. Mengapa Akurasi Random Forest Lebih Tinggi?

Random Forest sering kali memberikan akurasi yang lebih tinggi daripada *Decision Tree* tunggal karena beberapa alasan teknis berikut:

1.  **Mengatasi Overfitting**: Dengan melatih banyak pohon pada variasi data yang berbeda, kesalahan atau kecenderungan "menghafal data" (*overfitting*) pada satu pohon akan dinetralkan oleh prediksi benar dari pohon lainnya.
2.  **Stabilitas Tinggi**: Perubahan kecil pada dataset tidak akan mengubah hasil prediksi secara signifikan karena model bergantung pada suara mayoritas, bukan pada satu jalur logika saja.
3.  **Reduksi Varians**: Mekanisme agregasi dari banyak pohon menurunkan tingkat varians model secara drastis tanpa menaikkan biasnya, sehingga model lebih tangguh (*robust*) terhadap data baru.
4.  **Tangguh Terhadap Outlier**: Dampak dari data yang menyimpang (*outlier*) menjadi sangat minimal terhadap hasil akhir karena adanya mekanisme pemungutan suara massal.

## 4. Ringkasan Alur Kerja (Workflow)
`Excel Reader` $\rightarrow$ `Partitioning (90/10)` $\rightarrow$ **`Random Forest Learner`** $\rightarrow$ **`Random Forest Predictor`** $\rightarrow$ `Scorer`.