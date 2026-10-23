# Analisis Pengaruh Interval Agregasi Data terhadap Akurasi Prediksi Volume Trafik Jaringan pada Horizon Prediksi yang Sama

Repository ini memuat proposal penelitian dan ringkasan rencana eksperimen untuk tugas Riset Informatika. Penelitian membandingkan interval agregasi 10 menit dan 1 jam dalam memprediksi volume trafik jaringan pada horizon prediksi yang sama, yaitu satu jam ke depan.

> **Status:** Proposal / rencana penelitian. Belum ada hasil eksperimen yang diklaim dalam dokumen ini.

---

## Informasi Peneliti

- **Nama:** Muhammad Panandito Fatahillah
- **NPM / NIM:** 23081010200
- **Program Studi:** Informatika
- **Mata Kuliah:** Riset Informatika

---

## Ringkasan Proposal

### 1. Latar Belakang

Interval agregasi menentukan seberapa rapat suatu metrik trafik direkam dalam deret waktu. Data berinterval 10 menit dapat mempertahankan perubahan trafik jangka pendek, tetapi menghasilkan jumlah titik yang lebih banyak. Data berinterval 1 jam lebih ringkas, tetapi merangkum fluktuasi dalam periode yang lebih panjang. Konsekuensinya bagi akurasi prediksi volume trafik jaringan perlu diuji secara empiris.

Penelitian ini menggunakan dataset publik CESNET-TimeSeries24, yang menyediakan deret waktu trafik jaringan pada interval 10 menit, 1 jam, dan 1 hari. Dataset mencakup seri pada tingkat institusi, subnet institusi, dan alamat IP. Penelitian ini difokuskan pada data tingkat institusi pada interval 10 menit dan 1 jam, dengan jumlah byte (`n_bytes`) sebagai variabel target.

### 2. Judul dan Fokus

**Judul:** Analisis Pengaruh Interval Agregasi Data terhadap Akurasi Prediksi Volume Trafik Jaringan pada Horizon Prediksi yang Sama.

Fokus utama adalah membandingkan prediksi volume byte untuk **satu jam berikutnya** dari dua jenis data masukan:

- **Kondisi A:** interval data 10 menit. Riwayat enam jam berisi 36 titik; target satu jam dibentuk dari jumlah enam interval 10 menit berikutnya.
- **Kondisi B:** interval data 1 jam. Riwayat enam jam berisi 6 titik; targetnya adalah volume byte pada interval satu jam berikutnya.

Waktu prediksi dan periode pengujian kedua kondisi harus disejajarkan agar perbandingan adil.

### 3. Rumusan Masalah

1. Bagaimana perbedaan akurasi prediksi volume trafik jaringan antara interval agregasi 10 menit dan 1 jam, dengan horizon prediksi yang sama-sama satu jam?
2. Interval agregasi mana yang menghasilkan kesalahan prediksi lebih rendah berdasarkan MAE, RMSE, dan WAPE?
3. Apakah perbedaan kinerja konsisten pada beberapa seri trafik institusi atau hanya terlihat pada seri tertentu?

---

## Research Gap dan Kebaruan yang Diusulkan

Literatur yang ditinjau menunjukkan bahwa pengaruh agregasi temporal telah dikaji pada prediksi trafik transportasi; ada pula penelitian yang mengkaji prediksi trafik jaringan dengan memanfaatkan data bergranularitas kasar. CESNET-TimeSeries24 menyediakan beberapa interval agregasi untuk seri trafik jaringan komputer.

Kontribusi yang diusulkan bukan klaim bahwa efek agregasi belum pernah dipelajari. Fokusnya adalah perbandingan terkontrol pada volume byte trafik jaringan dengan:

1. **Horizon sama:** semua model memprediksi volume selama satu jam ke depan.
2. **Waktu uji selaras:** prediksi dari dua interval dibandingkan pada waktu target yang sama.
3. **Beberapa seri institusi:** hasil tidak hanya bergantung pada satu seri trafik.
4. **Baseline yang jelas:** Random Forest Regression dibandingkan dengan prediksi sederhana berdasarkan volume satu jam terakhir.
5. **Pelaporan keterbatasan:** hasil dibatasi pada data CESNET3 dan tidak langsung ditafsirkan sebagai peningkatan utilisasi link atau QoS.

Research gap ini masih bersifat awal dan perlu diperkuat melalui penelusuran literatur sistematis sebelum penelitian dijadikan publikasi.

---

## Dataset Riset

- **Nama:** CESNET-TimeSeries24
- **Sumber dataset:** [Zenodo - CESNET-TimeSeries24](https://zenodo.org/records/13382427)
- **Artikel dataset:** Koumar, J., Hynek, K., Čejka, T., & Šiška, P. (2025). [CESNET-TimeSeries24: Time Series Dataset for Network Traffic Anomaly Detection and Forecasting](https://doi.org/10.1038/s41597-025-04603-x). *Scientific Data*, 12, 338.
- **Sumber metrik:** seri tingkat institusi yang tersedia pada interval 10 menit dan 1 jam.
- **Variabel utama:** `n_bytes`, jumlah byte yang tercatat pada setiap interval agregasi.
- **Cakupan data yang dilaporkan pada artikel:** sekitar 40 minggu pemantauan; tersedia seri tingkat institusi, subnet institusi, dan IP.

Sebelum pemodelan, periksa kelengkapan, timestamp, duplikasi, dan keselarasan entitas antara dua interval. Nilai nol tidak otomatis dianggap data hilang. Jumlah institusi akhir yang dianalisis harus dicatat setelah proses pemeriksaan data selesai.

---

## Metodologi dan Metrik Evaluasi

1. **Pra-pemrosesan:** memuat seri institusi, menghubungkan `id_time` dengan waktu kalender, memeriksa kelengkapan, duplikasi, dan keselarasan waktu.
2. **Pembentukan fitur:** menggunakan riwayat enam jam sebelum waktu prediksi. Pada interval 10 menit terdapat 36 nilai lag; pada interval 1 jam terdapat 6 nilai lag.
3. **Pembentukan target:** pada data 10 menit, jumlahkan enam titik berikutnya untuk memperoleh volume satu jam. Pada data 1 jam, gunakan satu titik jam berikutnya.
4. **Pembagian data:** pembagian kronologis, dengan rancangan awal 70% periode awal untuk pelatihan, 15% berikutnya untuk validasi, dan 15% terakhir untuk pengujian. Rentang waktu yang sama diterapkan pada kedua kondisi.
5. **Model utama:** Random Forest Regression.
6. **Baseline:** prediksi volume satu jam berikutnya menggunakan volume satu jam terakhir.
7. **Evaluasi:** MAE, RMSE, dan WAPE. Laporkan hasil per seri dan ringkasan lintas seri.
8. **Analisis statistik:** jika data mencukupi, pertimbangkan uji Wilcoxon signed-rank pada kesalahan berpasangan per seri, dengan taraf signifikansi awal 0,05 dan disertai ukuran efek.

### Metrik

- **MAE:** rata-rata selisih absolut antara nilai aktual dan prediksi; satuan byte.
- **RMSE:** akar dari rata-rata kuadrat kesalahan; satuan byte dan lebih sensitif terhadap kesalahan besar.
- **WAPE:** total kesalahan absolut dibagi total volume aktual, dikalikan 100%.

WAPE tidak terdefinisi untuk seri yang total volume aktualnya nol pada periode uji; kondisi tersebut harus dilaporkan secara eksplisit.

---

## Kerangka Pemikiran

| Tahap | Aktivitas | Luaran |
|---|---|---|
| 1. Data | Memuat seri trafik institusi pada interval 10 menit dan 1 jam | Dataset sumber tercatat |
| 2. Pra-pemrosesan | Memeriksa kualitas dan keselarasan waktu | Seri yang memenuhi kriteria |
| 3. Pembentukan target | Membuat prediksi volume satu jam pada timestamp yang sama | Sampel eksperimen sebanding |
| 4. Pemodelan | Menjalankan Random Forest dan baseline | Prediksi model |
| 5. Evaluasi | Menghitung MAE, RMSE, WAPE dan menguji konsistensi antarseri | Tabel dan grafik hasil |
| 6. Kesimpulan | Menafsirkan trade-off resolusi data dan kesalahan prediksi | Temuan empiris sesuai ruang lingkup |

---

## Referensi Utama

1. Koumar, J., Hynek, K., Čejka, T., & Šiška, P. (2025). CESNET-TimeSeries24: Time Series Dataset for Network Traffic Anomaly Detection and Forecasting. *Scientific Data*, 12, 338. [https://doi.org/10.1038/s41597-025-04603-x](https://doi.org/10.1038/s41597-025-04603-x)
2. Weerasekera, R., Sridharan, M., & Ranjitkar, P. (2020). Implications of Spatiotemporal Data Aggregation on Short-Term Traffic Prediction Using Machine Learning Algorithms. *Journal of Advanced Transportation*, 2020, 7057519. [https://doi.org/10.1155/2020/7057519](https://doi.org/10.1155/2020/7057519)
3. Pavlyuk, D. (2020). Temporal Aggregation Effects in Spatiotemporal Traffic Modelling. *Sensors*, 20(23), 6931. [https://doi.org/10.3390/s20236931](https://doi.org/10.3390/s20236931)
4. Rusek, K., & Drton, M. (2023). Fine-grained Network Traffic Prediction from Coarse Data. *Austrian Journal of Statistics*, 52(3), 114-123. [https://doi.org/10.17713/ajs.v52i3.1471](https://doi.org/10.17713/ajs.v52i3.1471)
5. CESNET. CESNET-TimeSeries24 dataset. Zenodo. [https://zenodo.org/records/13382427](https://zenodo.org/records/13382427)

---

## Struktur Repository

```text
.
├── PROPOSAL_PENELITIAN.pdf   # Proposal penelitian (Bab I - Bab III dan daftar pustaka)
└── README.md                 # Ringkasan proposal, dataset, gap, dan metodologi
```

## Catatan Sebelum Pengumpulan

- Ganti placeholder nama, NPM/NIM, program studi, dan mata kuliah pada proposal PDF.
- Pastikan format proposal sesuai pedoman kampus.
- Verifikasi dan perluas tinjauan literatur serta research gap.
- Jangan mencantumkan hasil akurasi, signifikansi, atau klaim keunggulan interval sebelum eksperimen dijalankan.
