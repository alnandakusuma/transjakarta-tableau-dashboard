**Bahasa:** [English](README.md) | Bahasa Indonesia

# Dashboard Ridership & Pendapatan Transjakarta (Tableau)

Dashboard Tableau interaktif yang menelusuri jumlah perjalanan, pendapatan, jam sibuk, koridor dan halte tersibuk, serta profil penumpang. Data yang dipakai adalah dataset tap-in/tap-out Transjakarta yang **disimulasikan** (April 2023).

**Dashboard interaktif:** [Buka di Tableau Public](https://public.tableau.com/app/profile/alnanda.kusuma/viz/TransjakartaRidershipRevenueDashboard/RidershipOverview)

## Tampilan Dashboard

**1. Ridership Overview**
![Ridership Overview](screenshots/01-overview.png)

**2. Stops & Passengers**
![Stops and Passengers](screenshots/02-stops-passengers.png)

## Pertanyaan Bisnis
1. Berapa jumlah perjalanan dan pendapatan, dan bagaimana tren harian perjalanan?
2. Kapan jam sibuknya (hari kerja vs akhir pekan)?
3. Koridor dan halte naik mana yang paling ramai, dan di mana lokasinya di peta?
4. Bagaimana profil penumpang (kelompok usia, jenis kelamin, bank kartu)?
5. Seberapa lengkap datanya (tap-out kosong, durasi)?

## Dataset
- **Sumber:** [Transjakarta Transportation Transaction](https://www.kaggle.com/datasets/dikisahkan/transjakarta-transportation-transaction) di Kaggle (file sekitar 180 ribu baris; 189.500 baris dimuat)
- **Penting:** data transaksi bersifat **simulasi** (dibuat dengan Faker). Data halte dan koridor berasal dari feed GTFS publik Transjakarta. Temuan menggambarkan pola dalam data simulasi, bukan operasional Transjakarta yang sebenarnya.
- File CSV mentah tidak disertakan di repositori ini.

## Metrik Utama
| Metrik | Nilai |
|---|---|
| Total perjalanan | 189.500 |
| Total pendapatan | Rp496,6 juta (sekitar Rp2.620 per perjalanan) |
| Rata-rata durasi perjalanan | 72,1 menit |
| Perjalanan tanpa tap-out | 3,5% |

## Alur Pengerjaan
1. **Koneksi data:** memuat CSV di Tableau Public dan memperbaiki locale agar nilai desimal (koordinat, tarif) terbaca sebagai angka.
2. **Persiapan data:** mengganti nama field, mengatur tipe data, dan menyembunyikan field nama dan ID kartu agar pengenal bernuansa pribadi tidak ikut di extract yang dipublikasikan.
3. **Calculated field:** durasi perjalanan (menit), jam, hari, jenis hari, usia dan kelompok usia, jumlah perjalanan (distinct count), persentase tap-out kosong, rata-rata tarif per perjalanan.
4. **Worksheet:** empat kartu KPI, tren harian, heatmap jam × hari, 10 koridor teratas, peta halte, 10 halte naik teratas, usia dan jenis kelamin, pangsa bank kartu.
5. **Dashboard:** dua dashboard dengan ukuran tetap, aksi filter, dan tombol navigasi.

## Calculated Field Utama
```
Trip Duration (min) =
IF NOT ISNULL([Tap Out Time]) AND [Tap Out Time] >= [Tap In Time]
THEN DATEDIFF('minute',[Tap In Time],[Tap Out Time]) END

Hour of Day = DATEPART('hour',[Tap In Time])

Day Type =
IF DATEPART('weekday',[Tap In Time]) = 1 OR DATEPART('weekday',[Tap In Time]) = 7
THEN 'Weekend' ELSE 'Weekday' END

Age Group =
IF [Age] < 20 THEN '<20' ELSEIF [Age] < 30 THEN '20-29'
ELSEIF [Age] < 40 THEN '30-39' ELSEIF [Age] < 50 THEN '40-49'
ELSEIF [Age] < 60 THEN '50-59' ELSE '60+' END

Trips = COUNTD([Trans ID])
Missing Tap-Out % = SUM(IF ISNULL([Tap Out Time]) THEN 1 ELSE 0 END) / COUNT([Trans ID])
```

## Temuan Utama
- **Pola mingguan:** sekitar 8.000-10.000 perjalanan per hari kerja vs sekitar 2.000 pada akhir pekan. Dua hari pertama April hampir kosong karena data efektif mulai 3 April.
- **Jam sibuk (hari kerja):** puncak pagi sekitar pukul 06.00 (±5,6 ribu naik) dan puncak sore sekitar pukul 17.00 (±5,2 ribu).
- **Koridor:** perjalanan tersebar di banyak koridor; yang tersibuk sekitar 1,9 ribu perjalanan (sekitar 1% dari seluruh perjalanan).
- **Halte:** Penjaringan adalah halte naik tersibuk (±1,4 ribu), jauh di atas halte kedua.
- **Penumpang:** kelompok usia 30-39 paling banyak, 60+ paling sedikit. Penumpang perempuan menyumbang porsi perjalanan lebih besar daripada laki-laki di setiap kelompok usia.
- **Bank kartu:** kartu DKI menyumbang sekitar separuh perjalanan, disusul e-money.

## Keterbatasan
- Data bersifat simulasi dan hanya satu bulan, sehingga hasilnya untuk latihan dan bukan untuk keputusan operasional.
- Siang hari kerja (sekitar 10.00-15.00) jauh lebih sepi daripada akhir pekan. Hal ini tidak lazim pada angkutan umum nyata dan kemungkinan merupakan artefak pembuatan data.
- Usia dihitung dengan tahun referensi 2023.
- Tampilan terbaik di desktop (tata letak tetap 1200 × 900).

## Tools
Tableau Public

## Penulis
**Alnanda**
www.linkedin.com/in/alnandakusuma · https://github.com/alnandakusuma