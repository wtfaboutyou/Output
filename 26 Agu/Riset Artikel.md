# Mendeteksi Pola Aktivitas Mencurigakan dengan Data Analysis

Artikel ini akan membahas penggunaan Data Analysis dan Exploratory Data Analysis (EDA) untuk menemukan pola aktivitas jaringan yang tidak biasa. Dataset yang digunakan adalah UNSW-NB15 Testing Set, yang berisi data trafik jaringan normal dan berbagai kategori aktivitas serangan.

Analisis akan dilakukan menggunakan Python, Pandas, Matplotlib, dan Seaborn. Tahapannya meliputi pemahaman struktur dataset, pengecekan missing value dan data duplikat, analisis statistik, eksplorasi protokol, IP address, jumlah paket, jumlah byte, rate trafik, serta visualisasi distribusi data.

Salah satu fokus utama adalah mencari outlier dan membandingkan karakteristik trafik normal dengan trafik yang dikategorikan sebagai serangan. Dari hasil tersebut, akan dicari pola seperti aktivitas dengan jumlah paket atau byte yang jauh lebih tinggi, IP dengan frekuensi aktivitas tertentu, maupun karakteristik trafik lain yang berbeda dari mayoritas data.

Artikel ini tidak bertujuan membuat sistem deteksi serangan secara otomatis. Fokusnya adalah menunjukkan bagaimana Data Analysis dapat digunakan untuk mengeksplorasi data jaringan dan menemukan indikasi aktivitas yang perlu diperiksa lebih lanjut. Dengan pendekatan ini, materi EDA dapat diterapkan secara langsung pada konteks TJKT dan cyber security.

Sumber : https://www.kaggle.com/datasets/dhoogla/unswnb15
