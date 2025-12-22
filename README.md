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
