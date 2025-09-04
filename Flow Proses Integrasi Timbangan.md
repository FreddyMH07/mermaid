Flow Proses Integrasi Timbangan
flowchart TD
  A[Truk Masuk - Timbang Bruto] --> B[Truk Keluar - Timbang Tarra]
  B --> C[Hitung Netto = Bruto - Tarra]
  C --> D[Input Refraksi %, Fee Koperasi %, Biaya Angkut]
  D --> E[Aplikasi Timbangan kirim data ke Staging DB]
  E --> F[Integrator Mapping]
  F --> G[MAS ERP - Goods Receipt (GRN, Qty=Netto)]
  G --> H[MAS ERP - Purchase Invoice]
  H --> I{Cetak Faktur}
  I -->|Faktur A| J[Supplier/Petani]
  I -->|Faktur B| K[Koperasi]
  G --> L[Produksi / Max Balance (pakai Netto)]
  H --> M[Posting ke General Ledger]
  M --> N[Laporan & Rekonsiliasi Harian]
