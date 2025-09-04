flowchart TD
  A[Truk Masuk<br/>Timbang Bruto] --> B[Truk Keluar<br/>Timbang Tarra]
  B --> C[Hitung Netto<br/>Bruto minus Tarra]
  C --> D[Input Refraksi persen<br/>Fee Koperasi persen<br/>Biaya Angkut]
  D --> E[Aplikasi Timbangan<br/>kirim ke Staging DB]
  E --> F[Integrator Mapping<br/>hitung diskon fee angkut]
  F --> G[MAS ERP<br/>Goods Receipt GRN<br/>Qty Netto]
  G --> H[MAS ERP<br/>Purchase Invoice]
  H --> I{Cetak Faktur}
  I -->|Faktur A| J[Supplier atau Petani]
  I -->|Faktur B| K[Koperasi]
  G --> L[Produksi / Max Balance<br/>pakai Netto]
  H --> M[Posting ke General Ledger]
  M --> N[Laporan dan Rekonsiliasi Harian]
