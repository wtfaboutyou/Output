# Judul
Mencari Produk yang Sering Dibeli Bersamaan Menggunakan SQL

## Fokus
Menganalisis produk yang muncul dalam transaksi yang sama untuk menemukan pola pembelian antarproduk.

## Business Question
- Produk apa yang sering dibeli bersamaan?
- Berapa kali kombinasi produk tersebut muncul?
- Kombinasi mana yang paling sering terjadi?

## Konsep SQL
- Self JOIN
- JOIN berdasarkan `InvoiceNo`
- `GROUP BY`
- `COUNT(DISTINCT InvoiceNo)`
- `ORDER BY`

## Dataset
Online Retail

Kolom penting:
- `InvoiceNo` → ID transaksi
- `StockCode` → kode produk
- `Description` → nama produk
- `Quantity` → jumlah produk
- `InvoiceDate` → waktu transaksi
- `CustomerID` → ID pelanggan

## Output
Tabel pasangan produk dan jumlah transaksi tempat keduanya dibeli bersama.

Contoh:

| Product 1 | Product 2 | Transaksi Bersama |
|---|---|---:|
| Product A | Product B | 245 |
| Product C | Product D | 198 |

## Insight Bisnis
Hasilnya dapat digunakan untuk melihat peluang:
- Cross-selling
- Product bundling
- Rekomendasi produk

## Kategori
Business Analytics / Business Intelligence

## Dataset
https://archive.ics.uci.edu/dataset/352/online+retail
