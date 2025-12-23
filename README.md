# Garuda Sentiment: LLM (Gemini) vs Lexicon (InSet)

Studi komparatif mengenai performa Large Language Model (Gemini 2.5) melawan metode Lexicon tradisional (InSet) dalam menganalisis sentimen netizen sepak bola Indonesia terhadap pelatih John Herdman.

## Latar Belakang
Analisis sentimen pada media sosial Indonesia, khususnya dalam komunitas sepak bola, memiliki tantangan linguistik yang tinggi. Beberapa tantangan utama meliputi:
* **Sarkasme:** Penggunaan kata positif untuk mengekspresikan kritik.
* **Kalimat Perbandingan:** Membandingkan subjek dengan pihak lain secara implisit.
* **Variasi Slang:** Penggunaan singkatan dan kata tidak baku yang sangat dinamis.

Proyek ini mengevaluasi apakah metode klasik berbasis kamus (Lexicon) tetap relevan atau apakah AI modern (LLM) memberikan akurasi konteks yang jauh lebih baik dalam menangkap nuansa bahasa tersebut.

## Metodologi (Dual-Stream Pipeline)
Penelitian ini menggunakan dua jalur pemrosesan data yang berbeda:

| Fitur | Pendekatan Lexicon (InSet) | Pendekatan LLM (Gemini 2.5) |
| :--- | :--- | :--- |
| **Preprocessing** | **Heavy Normalization**: Konversi slang ke baku, penghapusan stopwords, dan case folding. | **Minimal Cleaning**: Mempertahankan tanda baca dan struktur kalimat untuk menjaga konteks emosi. |
| **Logika** | **Mathematical Scoring**: Penjumlahan bobot kata berdasarkan nilai tetap dalam kamus sentimen. | **Contextual Understanding**: Menggunakan pemrosesan bahasa alami untuk memahami ironi dan subjek. |
| **Karakteristik** | Statis dan bergantung sepenuhnya pada kelengkapan kamus. | Dinamis dan mampu memahami hubungan antar kata dalam satu kalimat. |

## Analisis Hasil dan Temuan Utama

### 1. Deteksi Sarkasme: Anomali Kata "Bagus"
Berdasarkan visualisasi Frekuensi Kata dan WordCloud, ditemukan bahwa kata **"bagus"** muncul secara dominan dalam kategori sentimen **Negative** pada model Gemini. Hal ini membuktikan kemampuan Gemini dalam mengidentifikasi sarkasme (contoh: "Strategi bagus untuk kalah"), sementara metode Lexicon cenderung melabelinya sebagai positif karena nilai intrinsik kata tersebut dalam kamus.

### 2. Bias Kategori Netral pada Lexicon
Model InSet menunjukkan kecenderungan tinggi untuk melabeli komentar sebagai **Neutral**. Hal ini sering terjadi pada kalimat yang mengandung harapan (seperti kata "semoga") atau penggunaan slang yang tidak terdaftar, sehingga sistem gagal memberikan skor dan menghasilkan nilai nol.

## Studi Kasus: Bukti Validasi (Row 336)
Ditemukan bukti nyata pada data dengan **ID 336** yang menunjukkan perbedaan signifikan dalam pengambilan keputusan:

**Teks Komentar:**
> *"pelatih Jhon herdman dari inggris di bandinkan sm si tayong... kualitas pelatih jauh masing lebih **bagus** si tayong."*

| Metode | Prediksi | Analisis |
| :--- | :--- | :--- |
| **InSet (Lexicon)** | **Positive** | Terkecoh oleh kata kunci "bagus" dan memberikan nilai positif secara otomatis. |
| **LLM (Gemini)** | **Negative** | Memahami konteks perbandingan. Memuji pelatih lama (STY) dalam konteks ini merupakan kritik terhadap pelatih saat ini. |

### *DATA LAINNYA*

ID | Teks Komentar | Sentiment (Gemini) | Sentiment (InSet)
| :--- | :--- | :---| :--- |
336 | pelatih Jhon herdman dari inggris di bandinkan sm si tayong \n kualitas pelatih jauh masing lebih bagus si tayong. | Negative | Positive
305 | Raportnya tidak lebih bagus dari sty""" | Negative | Negative
6 | Jangan pusing2, Erik n Towel aja suruh ngelatih.... biar tambah bagus timnasnya... | Negative | Negative

## Eksperimen Lanjutan: Pemodelan IndoBERT-base-p1
Setelah melakukan pelabelan, dilakukan fase pelatihan model menggunakan arsitektur IndoBERT-base-p1 untuk melihat bagaimana model Deep Learning mempelajari kedua jenis label tersebut.

### **Perbandingan Performa Metrik**
Berdasarkan hasil pelatihan selama 4 epoch, didapatkan perbedaan metrik yang kontras antara kedua model

Metrik Evaluasi | IndoBERT (Label InSet) | IndoBERT (Label Gemini)
| :--- | :--- | :---|
Akurasi Validasi | ~90.2% | ~71.1%
F1-Macro | ~0.90 | ~0.70
Kondisi Model | Stabil (Generalisasi Baik) | Overfitting (Loss Validasi Naik)

Model yang dilatih dengan label InSet memiliki skor lebih tinggi karena konsistensi label berbasis kata kunci yang lebih mudah dipelajari oleh model. Sebaliknya, model Gemini mengalami overfitting yang dipicu oleh adanya noise pada pelabelan awal serta jumlah epoch yang terlalu tinggi untuk dataset ini dan kurangnya dataset sehingga gagal memahami banyak konteks (sarkas, dll).

### **Validasi Manusia (Uji Coba 10 Sampel Campuran)**
Untuk menguji performa riil, kedua model diuji menggunakan 10 kalimat baru yang mengandung sarkasme, kata kasar, dan teks berita faktual.

**A. Analisis Sarkasme** Secara mengejutkan, model InSet-trained justru lebih unggul dalam mendeteksi sarkasme. Hal ini dikarenakan label InSet memberikan sinyal negatif yang sangat kuat pada kata-kata "jangkar" (seperti: "kalah", "bingung", "ampas") yang berhasil dipelajari model sebagai indikator utama sentimen negatif, meskipun terdapat kata "bagus" di dalamnya.

**B. Analisis Teks Netral (Berita/Fakta)** Model Gemini-trained menunjukkan keunggulan dalam mendeteksi teks netral atau informatif (seperti berita perpanjangan kontrak). Sebaliknya, model InSet-trained gagal mengidentifikasi kategori netral dan cenderung melabeli berita faktual sebagai sentimen positif atau negatif secara salah.

## **Kesimpulan Eksperimen Pemodelan**
1. IndoBERT + InSet: Sangat tangguh dalam mengenali emosi ekstrem dan sarkasme berbasis kata kunci negatif yang kuat, namun tidak mampu membedakan teks berita (netral).
2. IndoBERT + Gemini: Memiliki pemahaman spektrum bahasa yang lebih luas (mampu mendeteksi teks netral), namun memerlukan pembersihan label (label cleaning) dan regulasi pelatihan yang lebih ketat untuk menangkap konteks sarkasme secara akurat.

## Sumber Daya dan Referensi
Proyek ini mengintegrasikan repositori dan dataset open-source berikut:

* **Sentiment Lexicon (InSet):** Menggunakan kamus [InSet (Indonesian Sentiment Lexicon)](https://github.com/fajri91/InSet) oleh Fajri Koto.
* **Stopwords Removal:** Daftar kata umum diambil dari [ID-Stopwords](https://github.com/masdevid/ID-Stopwords) oleh Mas Devid.
* **Text Normalization:** Kamus konversi slang merujuk pada [NormalisasiKata](https://github.com/evrintobing17/NormalisasiKata) oleh Evrin Tobing.

## Teknologi Utama
* **Python 3.10+**
* **Google Generative AI SDK (Gemini 2.5 Flash Lite)**
* **Pandas & NumPy**
* **Matplotlib & Seaborn**
* **WordCloud**

## Cara Penggunaan
1. Clone repository ini ke direktori lokal.
2. Instalasi library melalui perintah: `pip install -r requirements.txt`.
3. Konfigurasi API Key Gemini pada file skrip utama.
4. Jalankan Jupyter Notebook atau file Python untuk melihat proses pelabelan dan visualisasi.

---

**Author**
**Muhammad Habib Nur Aiman**
Data Science Student.
