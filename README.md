# Olist E-Commerce: Exploratory Data Analysis

Analisis data transaksional Olist (marketplace e-commerce terbesar di Brasil) untuk menjawab pertanyaan bisnis seputar pertumbuhan, produk, pengiriman, geografi, dan perilaku pelanggan.

## Dashboard

[Lihat Dashboard di Tableau Public](https://public.tableau.com/views/Book1_17727695494990/Dashboard1?:language=en-GB&publish=yes&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

## Pertanyaan Bisnis

| # | Pertanyaan | Dijawab Di |
|---|-----------|-----------|
| 1 | Bagaimana tren pertumbuhan bisnis Olist dari waktu ke waktu? | [04_eda](notebooks/04_eda.ipynb) Bagian 1, Dashboard |
| 2 | Kategori produk apa yang paling menguntungkan dan bagaimana karakteristiknya? | [04_eda](notebooks/04_eda.ipynb) Bagian 2, Dashboard |
| 3 | Apa faktor utama yang menyebabkan keterlambatan pengiriman? | [04_eda](notebooks/04_eda.ipynb) Bagian 3, [05_stat](notebooks/05_statistical_analysis.ipynb) Uji 1-2 |
| 4 | Bagaimana distribusi geografis pelanggan mempengaruhi biaya dan waktu pengiriman? | [04_eda](notebooks/04_eda.ipynb) Bagian 4, [05_stat](notebooks/05_statistical_analysis.ipynb) Uji 3-5 |
| 5 | Bagaimana perilaku pembelian pelanggan dan peluang retensi? | [04_eda](notebooks/04_eda.ipynb) Bagian 5, 7, 8, [05_stat](notebooks/05_statistical_analysis.ipynb) Uji 6 |

## Temuan Utama

### Pertumbuhan Bisnis
- Order tumbuh dari Sep 2016 hingga puncak **Nov 2017 (7.186 order)**, kemungkinan efek Black Friday.
- Total revenue mencapai **13+ juta BRL** selama periode data.

### Produk
- Top 5 kategori: `cama_mesa_banho`, `beleza_saude`, `esporte_lazer`, `moveis_decoracao`, `informatica_acessorios`.
- Distribusi harga sangat skewed — mayoritas produk di bawah 200 BRL.

### Pengiriman
- Rata-rata pengiriman **12,6 hari** (estimasi 23,7 hari — terlalu konservatif).
- **8,1% order terlambat** — produk yang terlambat cenderung lebih berat (p < 0.001) dan lebih mahal (p < 0.001).

### Geografi
- **SP mendominasi** dengan 40.488 order.
- Waktu kirim non-SP **hampir 2x lipat** dibanding SP (median 12,9 vs 7,2 hari, p < 0.001).
- Ongkir berbeda signifikan antar state (p < 0.001).

### Perilaku Pelanggan
- Peak pembelian: **Senin, jam 16:00**. Weekend hanya 23% transaksi.
- Rata-rata **1,14 item per order** — peluang besar untuk cross-selling.
- Mayoritas pelanggan adalah **one-time buyer** (RFM analysis).

## Struktur Proyek

```
olist-ecommerce-eda/
├── dashboard/                            # Tableau workbook
├── data/
│   ├── raw/                              # CSV mentah dari Kaggle
│   ├── database/                         # SQLite database (olist.db)
│   ├── clean/                            # CSV hasil cleaning
│   └── tableau/                          # CSV untuk Tableau dashboard
├── notebooks/
│   ├── 01_load_to_sqlite.ipynb           # Muat CSV ke SQLite
│   ├── 02_sql_exploration.ipynb          # Eksplorasi awal (kualitas data)
│   ├── 03_cleaning.ipynb                 # Data cleaning
│   ├── 04_eda.ipynb                      # EDA (SQL + Python)
│   ├── 05_statistical_analysis.ipynb     # Uji hipotesis
│   └── 06_export_tableau.ipynb           # Export data untuk Tableau
└── README.md
```

## Alur Kerja

```
01_load_to_sqlite → 02_sql_exploration → 03_cleaning → 04_eda → 05_statistical_analysis → 06_export_tableau → Dashboard
```

Jalankan notebook secara berurutan. Setiap notebook bergantung pada output notebook sebelumnya.

## Dataset

[Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) — Kaggle.

Tabel yang digunakan:
- `orders` — 99.441 order (setelah cleaning: 96.455)
- `order_item` — 112.650 item (setelah cleaning: 108.628)
- `products` — 32.951 produk (setelah cleaning: 32.336)
- `customers` — 99.441 customer (setelah cleaning: 96.455)

## Metodologi

| Tahap | Teknik | Tools |
|-------|--------|-------|
| Penyimpanan | SQLite database | `sqlite3` |
| Eksplorasi | SQL query (NULL, duplikat, outlier, relasi) | `pandas` + SQL |
| Cleaning | Filter status, drop NULL, konversi tipe data, sinkronisasi tabel | `pandas` |
| EDA | SQL aggregasi + visualisasi Python | `matplotlib`, `seaborn` |
| Segmentasi | RFM Analysis (Recency, Frequency, Monetary) | `pandas` |
| Inferensi | Mann-Whitney U, Kruskal-Wallis, Spearman, Chi-Square | `scipy.stats` |
| Dashboard | Denormalized export, KPI, tren, kategori | `pandas` → Tableau |

## Hasil Uji Hipotesis

| Uji | Pertanyaan | p-value | Keputusan |
|-----|-----------|---------|-----------|
| 1 | Harga order terlambat vs tepat waktu | < 0.001 | Signifikan — order terlambat cenderung lebih mahal |
| 2 | Berat produk vs keterlambatan | < 0.001 | Signifikan — produk terlambat lebih berat |
| 3 | Ongkir antar state | < 0.001 | Signifikan — lokasi sangat mempengaruhi ongkir |
| 4 | Korelasi berat vs ongkir | < 0.001 | Signifikan — korelasi sedang (rho = 0.45) |
| 5 | Waktu kirim SP vs non-SP | < 0.001 | Signifikan — non-SP jauh lebih lama |
| 6 | Order weekday vs weekend | < 0.001 | Signifikan — weekend lebih sedikit dari expected |

## Rekomendasi Bisnis

1. **Optimasi estimasi pengiriman** — estimasi saat ini terlalu konservatif (23,7 hari vs aktual 12,6 hari). Perbaiki model estimasi untuk meningkatkan kepercayaan pelanggan.
2. **Ekspansi gudang distribusi** — waktu kirim non-SP hampir 2x lipat. Pertimbangkan warehouse di region RJ, MG, atau RS untuk mengurangi waktu dan biaya.
3. **Program retensi pelanggan** — mayoritas one-time buyer. Implementasi loyalty program, email re-engagement, dan bundle/cross-selling (rata-rata hanya 1,14 item/order).
4. **Strategi promosi berdasarkan waktu** — fokuskan campaign di hari Senin dan jam 14-17 saat traffic tertinggi.
5. **Perhatian khusus produk berat** — produk berat lebih rentan terlambat. Pertimbangkan dedicated logistics atau free shipping untuk produk ringan.

## Requirements

```
pandas
matplotlib
seaborn
scipy
```

## Cara Menjalankan

```bash
# Clone repository
git clone https://github.com/fikrifaizz/olist-ecommerce-eda.git
cd olist-ecommerce-eda

# Install dependencies
pip install pandas matplotlib seaborn scipy

# Jalankan notebook secara berurutan (01 → 06)
jupyter notebook
```