---
name: "pro-data-visualization"
description: "Gunakan saat membuat, merapikan, atau me-review grafik/dashboard (matplotlib, seaborn, plotly) agar tampil profesional: pilihan chart, warna, label, anotasi, dan aksesibilitas."
---

# Visualisasi Data Profesional

Tujuan: setiap grafik harus menyampaikan **satu pesan** dengan jelas, jujur, dan terlihat seperti dibuat analis senior. Bahasa label/judul mengikuti bahasa user (default Bahasa Indonesia).

## Alur kerja

1. **Tentukan pesan.** Sebelum menulis kode, rumuskan: pertanyaan apa yang dijawab? siapa audiensnya? apa temuan utamanya? Jika user tidak menyebutkan, simpulkan dari data lalu sebutkan asumsinya.
2. **Pilih jenis chart** (lihat tabel).
3. **Siapkan data**: urutkan, agregasi, hitung persentase/n, buang kategori yang tidak perlu.
4. **Terapkan tema** (lihat template) lalu gambar.
5. **Polish**: judul berisi temuan, label langsung, anotasi, sumber.
6. **Cek ulang** dengan checklist di bawah dan, bila memungkinkan, simpan ke PNG lalu lihat hasilnya (Read pada gambar) sebelum diserahkan.

## Memilih jenis chart

| Tujuan | Chart | Catatan |
|---|---|---|
| Tren waktu | Line | Maks 4-5 garis; beri label langsung di ujung garis |
| Perbandingan kategori | Bar (horizontal jika label panjang) | Urutkan besar ke kecil, kecuali kategori punya urutan alami |
| Komposisi | Stacked bar 100% / donut | Donut/pie hanya jika kategori <= 5 |
| Distribusi | Histogram, box/violin, ECDF | Tampilkan n dan median |
| Hubungan 2 variabel | Scatter (+ regresi/LOWESS) | Gunakan alpha untuk data padat |
| Perubahan antar dua titik | Slope chart / dumbbell | Lebih jelas daripada grouped bar |
| Kontribusi bertahap | Waterfall | Warna beda untuk naik/turun |
| Geografis | Choropleth | Normalisasi per kapita bila perlu |

Hindari: 3D, pie banyak irisan, dual axis yang menyesatkan, rainbow/jet colormap, efek bayangan.

## Aturan integritas data

- Sumbu Y bar chart **wajib mulai dari nol**. Line chart boleh tidak dari nol, tapi jangan dilebih-lebihkan.
- Skala sama (`sharey`/`sharex`) bila grafik dibandingkan berdampingan.
- Cantumkan satuan, periode, dan sumber data.
- Tampilkan ukuran sampel (`n=`) bila menampilkan proporsi/rata-rata antar segmen; segmen kecil bisa menyesatkan.
- Jangan memotong data atau memilih rentang waktu agar tren terlihat dramatis.

## Aturan warna

- **Kategorikal:** warna berbeda dengan tingkat terang mirip, maks 5-6.
- **Sekuensial:** satu hue, terang = rendah, gelap = tinggi.
- **Divergen:** dua hue berlawanan bertemu di netral pada titik tengah bermakna (0/target).
- **Sorotan:** abu-abu (`#b8bcc2`) untuk konteks, satu warna aksen untuk fokus.
- Warna konsisten antar grafik: satu kategori = satu warna di seluruh laporan.
- Jangan hanya merah-hijau; gunakan palet ramah buta warna (Okabe-Ito, ColorBrewer, viridis).
- Teks terhadap latar: kontras minimal 4,5:1. Label putih di atas warna terang (kuning/oranye muda) diganti gelap.
- Jangan pakai gradasi terang-gelap untuk data kategorikal.

Palet default:

```python
PALETTE = {
    "blue":   "#2a78d6",
    "orange": "#eda100",
    "red":    "#e34948",
    "green":  "#2f9e6f",
    "purple": "#7b5ea7",
    "gray":   "#b8bcc2",
    "ink":    "#1f2933",
    "muted":  "#6b7280",
}
# Okabe-Ito (colorblind-safe) untuk kategori banyak:
OKABE_ITO = ["#0072B2", "#E69F00", "#009E73", "#D55E00", "#CC79A7", "#56B4E9", "#F0E442"]
```

## Tipografi dan tata letak

- Hierarki: judul (bold, 15-16pt, rata kiri) > subjudul (muted, 11-12pt) > label sumbu (10-11pt) > sumber (8-9pt, muted).
- Judul menyatakan **temuan** ("Churn tertinggi pada paket Basic"), bukan deskripsi generik ("Churn per paket"). Subjudul menjelaskan metrik, satuan, periode.
- Rata kiri untuk judul dan anotasi; whitespace lega; margin konsisten.
- Hindari teks diputar >30 derajat; pakai bar horizontal jika label panjang.
- Format angka: pemisah ribuan, persen konsisten, satuan ringkas (rb, jt, M). Untuk locale Indonesia, pertimbangkan titik sebagai pemisah ribuan.
- Legend: hindari bila bisa memberi label langsung; jika perlu, taruh di atas tanpa kotak.
- Dashboard/multi-panel: metrik terpenting di kiri atas, panel sejajar, `sharey` bila relevan, judul utama (`suptitle`) + judul panel singkat.

## Template matplotlib (tema profesional)

```python
import matplotlib.pyplot as plt
import matplotlib as mpl

def set_pro_theme():
    mpl.rcParams.update({
        "figure.dpi": 120,
        "savefig.dpi": 200,
        "figure.facecolor": "white",
        "axes.facecolor": "white",
        "font.family": "sans-serif",
        "font.size": 11,
        "axes.titlesize": 14,
        "axes.titleweight": "bold",
        "axes.titlelocation": "left",
        "axes.labelcolor": "#374151",
        "axes.edgecolor": "#bbbbbb",
        "axes.spines.top": False,
        "axes.spines.right": False,
        "axes.spines.left": False,
        "axes.grid": True,
        "axes.grid.axis": "y",
        "grid.color": "#e5e7eb",
        "grid.linewidth": 0.8,
        "axes.axisbelow": True,
        "xtick.color": "#4b5563",
        "ytick.color": "#4b5563",
        "xtick.major.size": 0,
        "ytick.major.size": 0,
        "legend.frameon": False,
    })

def add_header(fig, title, subtitle=None, x=0.01):
    fig.text(x, 0.98, title, fontsize=16, fontweight="bold", ha="left", va="top", color="#1f2933")
    if subtitle:
        fig.text(x, 0.93, subtitle, fontsize=11, ha="left", va="top", color="#6b7280")

def add_source(fig, text, x=0.01, y=0.01):
    fig.text(x, y, text, fontsize=8.5, ha="left", va="bottom", color="#9ca3af")
```

Contoh stacked bar 100% dengan label bersih:

```python
set_pro_theme()
fig, ax = plt.subplots(figsize=(10, 5.5))
bottom = np.zeros(len(df))
for tier, col in tier_columns.items():
    vals = df[col].to_numpy()
    bars = ax.bar(df["segment"], vals, bottom=bottom, color=tier_colors[tier],
                  label=tier, width=0.65, edgecolor="white", linewidth=1.2, zorder=3)
    ax.bar_label(bars, labels=[f"{v:.0f}%" if v >= 6 else "" for v in vals],
                 label_type="center", color=label_colors[tier], fontsize=10, fontweight="bold")
    bottom += vals
ax.set_ylim(0, 100)
ax.yaxis.set_major_formatter(mpl.ticker.PercentFormatter())
add_header(fig, "Judul berisi temuan", "Proporsi customer per tier (%), periode ...")
add_source(fig, "Sumber: ... | n = ...")
fig.tight_layout(rect=[0, 0.04, 1, 0.88])
```

Teknik polish yang sering dipakai:
- **Highlight:** warnai satu bar dengan aksen, sisanya abu-abu.
- **Label langsung** pada ujung garis: `ax.text(x_last + pad, y_last, name, color=color, va="center")`.
- **Anotasi** peristiwa penting: `ax.annotate(text, xy=..., xytext=..., arrowprops=dict(arrowstyle="-", color="#9ca3af"))`.
- **Garis referensi** (target/rata-rata): `ax.axhline(y, color="#6b7280", ls="--", lw=1)` dengan label teks di ujung.
- **Panel kecil (small multiples)** lebih baik daripada satu grafik dengan terlalu banyak seri.

## Plotly / seaborn

- Plotly: `template="plotly_white"`, hilangkan `modebar` pada laporan statis, atur `hovertemplate` agar informatif, gunakan `title_x=0`.
- Seaborn: panggil `set_pro_theme()` setelah `sns.set_theme(...)`, atau atur `sns.despine(left=True)`.

## Output dan penyerahan

- Simpan dengan `fig.savefig("nama.png", dpi=200, bbox_inches="tight")`; untuk dokumen cetak sediakan juga SVG/PDF.
- Ukuran standar: 10x5.5 (satu grafik), 16x9 (slide), 16x10 (2x2 panel).
- Setelah menyerahkan, ringkas dalam 1-3 kalimat: temuan utama dan keputusan desain yang penting. Jangan memaparkan seluruh langkah.

## Checklist akhir

- [ ] Pesan utama terbaca dalam 5 detik?
- [ ] Jenis chart sesuai tujuan; urutan kategori logis?
- [ ] Sumbu jujur (bar mulai nol, skala konsisten)?
- [ ] Judul berisi temuan, ada subjudul (metrik, satuan, periode) dan sumber?
- [ ] Warna <= 5-6, bermakna, konsisten, ramah buta warna, kontras teks cukup?
- [ ] Tidak ada elemen dekoratif (border, bayangan, gridline tebal, 3D)?
- [ ] Label tidak bertumpuk; angka kecil disembunyikan atau dipindah; format angka rapi?
- [ ] n/ukuran sampel ditampilkan bila relevan?
- [ ] Hasil sudah dilihat sebagai gambar dan terbaca di ukuran kecil?