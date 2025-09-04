```mermaid
flowchart TB
  %% =======================
  %% LANE 1: GATE & QUEUE
  %% =======================
  subgraph L1[Gate dan Antrian]
    A0([Start])
    A1[Scan barcode antrian di gerbang BMT]
    A2{Barcode antrian valid}
    A3[Registrasi kendaraan dan supplier]
    A4{Slot timbangan tersedia}
    A5[Antrian menunggu panggilan]
  end

  A0 --> A1 --> A2
  A2 -- Ya --> A3 --> A4
  A2 -- Tidak --> A1
  A4 -- Ya --> A6[Menuju timbangan]
  A4 -- Tidak --> A5 --> A4

  %% =======================
  %% LANE 2: WEIGHBRIDGE
  %% =======================
  subgraph L2[Timbangan]
    B1[Scan barcode timbangan]
    B2{Ticket timbangan duplikat}
    B3[Generate wb_ticket_id]
    B4[Truck in timbang bruto]
    B5[Truck out timbang tarra]
    B6[Hitung netto = bruto minus tarra]
    B7[Input refraksi persen dan komponennya]
    B8[Input harga per kg dan fee koperasi persen]
    B9[Input biaya angkut per tiket]
    B10{Netto lebih dari nol}
    B11([Lock ticket])
  end

  A6 --> B1 --> B2
  B2 -- Ya --> B1
  B2 -- Tidak --> B3 --> B4 --> B5 --> B6 --> B7 --> B8 --> B9 --> B10
  B10 -- Tidak --> B4
  B10 -- Ya --> B11

  %% =======================
  %% LANE 3: INTEGRATION
  %% =======================
  subgraph L3[Integration Layer]
    C1[POST ticket ke staging db]
    C2{Payload valid dan idempotent}
    C3([Store status NEW])
    C4[Transformer hitung nilai finansial<br/>gross line diskon refraksi subtotal fee koperasi biaya angkut total]
    C5([Mark status LOCKED])
    C6{Koneksi ERP tersedia}
  end

  B11 --> C1 --> C2
  C2 -- Tidak --> C1
  C2 -- Ya --> C3 --> C4 --> C5 --> C6

  %% =======================
  %% LANE 4: MAS ERP
  %% =======================
  subgraph L4[MAS ERP]
    D1[Create GRN qty = netto<br/>item BB SINGKONG]
    D2{GRN berhasil}
    D3[Create Purchase Invoice<br/>line item dengan diskon refraksi<br/>line fee koperasi negatif<br/>line biaya angkut positif]
    D4{Invoice berhasil}
    D5[Cetak Faktur A untuk supplier]
    D6[Cetak Faktur B untuk koperasi]
    D7([Posting ke buku besar GL])
  end

  C6 -- Ya --> D1 --> D2
  C6 -- Tidak --> C6
  D2 -- Tidak --> D1
  D2 -- Ya --> D3 --> D4
  D4 -- Tidak --> D3
  D4 -- Ya --> D5 & D6 --> D7

  %% =======================
  %% LANE 5: PRODUCTION
  %% =======================
  subgraph L5[Produksi]
    E1[Input proses produksi pakai qty netto]
    E2[Output tepung]
    E3[Output onggok]
    E4[Output limbah cair]
    E5[Losses tumpah atau evaporasi]
    E6([Total output harian])
  end

  D1 --> E1 --> E2 & E3 & E4 & E5 --> E6

  %% =======================
  %% LANE 6: REPORTING
  %% =======================
  subgraph L6[Max Balance dan Rekonsiliasi]
    F1[Hitung max balance<br/>input netto vs total output]
    F2{Selisih lebih besar dari 0.5 persen}
    F3[Alert investigasi QC atau produksi]
    F4([Normal])
    F5[Rekonsiliasi harian<br/>sum netto ticket vs sum qty GRN vs sum qty invoice]
    F6{Rekonsiliasi match}
    F7([Selesai hari ini])
  end

  E6 --> F1 --> F2
  F2 -- Ya --> F3 --> F5
  F2 -- Tidak --> F4 --> F5
  F5 --> F6
  F6 -- Ya --> F7
  F6 -- Tidak --> F3

  %% END

