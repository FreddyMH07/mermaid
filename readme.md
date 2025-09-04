```mermaid
classDiagram
    class PurchaseInvoice {
      - Singkong Netto : 10.000 Kg × Rp 2.000
      - Diskon Refraksi : 8% (-Rp 1.600.000)
      - Subtotal : Rp 18.400.000
      - Fee Koperasi : 5% (-Rp 920.000)
      - Biaya Angkut : Rp 150.000
      + Total Invoice : Rp 17.630.000
    }

    class FakturA {
      + Untuk Supplier/Petani
      + Nilai setelah Refraksi : Rp 18.400.000
    }

    class FakturB {
      + Untuk Koperasi
      + Nilai Fee : Rp 920.000
    }

    PurchaseInvoice --> FakturA
    PurchaseInvoice --> FakturB

