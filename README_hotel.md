# 🏨 Hotel Room Upgrade Prediction — Post Peak Season

**Tools:** Python (Pandas, Scikit-learn, SHAP, Streamlit)  
**Dataset:** Hotel Booking Demand Dataset — data historis reservasi hotel multi-properti  
**Author:** Mulyadi | [LinkedIn](https://linkedin.com/in/justmulyadi) | [GitHub](https://github.com/Mulyadi-Leo)

---

## 🎯 Latar Belakang

Setelah peak season berakhir, hotel menghadapi tantangan yang sama hampir setiap tahun: tingkat okupansi turun, dan revenue ikut tertekan.

Salah satu strategi yang sering dipakai adalah upgrade kamar — tapi selama ini upgrade sering diberikan secara gratis tanpa segmentasi yang jelas. Akibatnya, nilai kamar turun dan peluang bisnis terlewatkan.

Pertanyaan yang ingin dijawab:

> *"Bisakah kita memprediksi tamu mana yang paling berpotensi melakukan upgrade berbayar — sebelum mereka check-in?"*

Kalau bisa, hotel tidak perlu lagi menawarkan upgrade ke semua orang. Cukup ke tamu yang memang berpotensi — lebih efisien, lebih menguntungkan.

---

## ❓ Pertanyaan Bisnis

1. Karakteristik tamu seperti apa yang paling cenderung menerima upgrade?
2. Apakah saluran pemesanan (Direct, Corporate, TA/TO) mempengaruhi peluang upgrade?
3. Seberapa jauh jarak waktu pemesanan (lead time) mempengaruhi keputusan upgrade?
4. Segmen tamu mana yang paling potensial untuk ditawarkan upgrade berbayar?

---

## 🗄️ Dataset

Dataset berisi data historis reservasi dari beberapa properti hotel dengan fitur utama:

| Kolom | Penjelasan |
|-------|------------|
| `lead_time` | Jarak hari antara pemesanan dan check-in |
| `stays_in_week_nights` | Jumlah malam menginap di hari kerja |
| `stays_in_weekend_nights` | Jumlah malam menginap di akhir pekan |
| `adults`, `children`, `babies` | Komposisi tamu |
| `meal` | Paket makan yang dipilih |
| `market_segment` | Segmen pasar (Online TA, Corporate, Direct, dll) |
| `distribution_channel` | Saluran distribusi pemesanan |
| `is_repeated_guest` | Apakah tamu pernah menginap sebelumnya |
| `reserved_room_type` | Tipe kamar yang dipesan |
| `assigned_room_type` | Tipe kamar yang diberikan saat check-in |
| `total_of_special_requests` | Jumlah permintaan khusus tamu |
| `customer_type` | Tipe pelanggan (Transient, Group, Contract) |
| `adr` | Average Daily Rate — harga rata-rata per malam |

> **Target variabel:** Room upgrade — didefinisikan dari perbedaan antara `reserved_room_type` dan `assigned_room_type`.

---

## 🔍 Pendekatan & Metodologi

### Tantangan Utama: Class Imbalance
Mayoritas tamu tidak melakukan upgrade — sehingga model yang naif akan selalu memprediksi "tidak upgrade" dan tetap terlihat akurat. Ini tidak berguna secara bisnis.

**Solusi yang diterapkan:**
- SMOTE (Synthetic Minority Oversampling Technique) untuk menyeimbangkan distribusi kelas
- Evaluasi dengan **F1-score dan ROC-AUC**, bukan hanya accuracy
- **Threshold tuning** untuk mengoptimalkan keputusan berdasarkan konteks bisnis
- **Mean encoding** untuk fitur kategorikal
- **SHAP values** untuk interpretasi model yang lebih transparan

### Model yang Digunakan
Random Forest Classifier dengan hyperparameter tuning via GridSearchCV.

---

## 📊 Hasil & Temuan

### Feature Importance (SHAP Analysis)

| Fitur | Pengaruh | Insight |
|-------|----------|---------|
| `lead_time` | Sangat Tinggi | Kontribusi hampir 50% — jarak waktu pesan sangat menentukan |
| `stays_in_week_nights` | Tinggi | Durasi menginap di hari kerja berpengaruh besar |
| `stays_in_weekend_nights` | Cukup Tinggi | Durasi di akhir pekan juga relevan |
| `total_of_special_requests` | Menengah | Tamu yang banyak request cenderung lebih terbuka upgrade |
| `distribution_channel` | Menengah | Saluran pemesanan mempengaruhi peluang upgrade |
| `adults`, `customer_type` | Rendah-Menengah | Memberikan kontribusi tapi tidak dominan |
| `babies`, `is_repeated_guest` | Sangat Rendah | Hampir tidak digunakan model |

---

### Insight Bisnis

**1. Last-minute booking paling potensial untuk upgrade berbayar**
Tamu yang memesan < 5 hari sebelum check-in memiliki upgrade rate 20.56% — mereka dalam posisi fleksibel dan lebih impulsif saat tiba.

**2. Direct dan Corporate lebih sering di-upgrade**
Meski TA/TO mendominasi jumlah reservasi, tamu dari channel Direct dan Corporate secara proporsional lebih sering mendapat upgrade — segmen ini lebih berpotensi untuk penawaran berbayar.

**3. Tamu yang di-upgrade cenderung menginap lebih singkat**
Rata-rata durasi menginap tamu yang di-upgrade lebih pendek — weekdays 2.01 malam, weekend 0.70 malam. Ini membuka peluang strategi "short stay, premium comfort."

**4. Family memiliki peluang upgrade tertinggi**
Segmen Family memiliki upgrade rate 14.41% — jauh di atas Group yang tidak pernah di-upgrade. Penawaran berbasis kenyamanan keluarga lebih efektif.

---

## 💡 Rekomendasi Strategis

| Prioritas | Strategi | Target Segmen |
|-----------|----------|---------------|
| 🔴 Tinggi | Tawarkan upgrade berbayar saat check-in untuk last-minute booking | Tamu dengan lead time < 5 hari |
| 🔴 Tinggi | Buat paket upgrade eksklusif di channel Direct dan Corporate | Direct booker & corporate partner |
| 🟡 Sedang | Kampanye "Short Stay, Big Comfort" untuk tamu 1 malam | Solo, Couple, Family pendek |
| 🟡 Sedang | Penawaran berbasis emosi dan kenyamanan untuk segmen Family | Family dengan anak |
| 🟢 Rutin | Hindari upgrade gratis massal — alokasikan ke segmen berpotensi | Semua segmen |

---

## 🚀 Demo Aplikasi

Project ini dilengkapi aplikasi interaktif menggunakan **Streamlit** — rekruter atau hotel manager bisa langsung input data tamu dan melihat prediksi upgrade-nya secara real-time.

Untuk menjalankan secara lokal:

```bash
pip install -r requirements.txt
streamlit run app_streamlit.py
```

---

## 📁 Struktur Repository

```
room-upgrade-prediction-post-peak/
│
├── README.md
├── Upgrade_Room_Analysis.ipynb     ← Notebook analisis lengkap
├── app_streamlit.py                ← Aplikasi demo Streamlit
├── deploy.py                       ← Script deployment
├── rf_model_final.pkl              ← Model Random Forest final
├── mean_encoding_maps.pkl          ← Encoding untuk fitur kategorikal
└── requirements.txt
```

---

## 🛠️ Tech Stack

- **Python** — Pandas, NumPy, Scikit-learn
- **Imbalanced Learning** — SMOTE via imbalanced-learn
- **Model Interpretability** — SHAP
- **Visualization** — Matplotlib, Seaborn
- **Deployment** — Streamlit

---

## 💼 Skills yang Didemonstrasikan

- Supervised machine learning untuk klasifikasi binary
- Penanganan imbalanced dataset dengan SMOTE
- Model evaluation beyond accuracy (F1, ROC-AUC, threshold tuning)
- Model interpretability dengan SHAP values
- Translasi hasil model ke rekomendasi bisnis yang actionable
- Deployment model ke aplikasi interaktif berbasis Streamlit
