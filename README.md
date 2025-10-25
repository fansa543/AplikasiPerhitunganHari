# 🗓️ Aplikasi Perhitungan Hari (Tugas 4)

## 📘 Deskripsi Program
Aplikasi ini dibuat menggunakan **Java Swing (NetBeans)** untuk menghitung jumlah hari pada bulan dan tahun tertentu.  
Fitur tambahan meliputi:
- Menampilkan hari pertama dan terakhir dari bulan yang dipilih.
- Menghitung selisih hari antara dua tanggal menggunakan dua `JCalendar`.
- Sinkronisasi otomatis antara input bulan/tahun dengan kalender.

---

## 🧱 Komponen GUI
- JFrame: Wadah utama aplikasi.
- JPanel: Pengatur tata letak dan pewarnaan.
- JLabel: Label teks hasil dan penjelasan.
- JComboBox: Pilihan bulan.
- JSpinner: Input tahun.
- JButton: Tombol aksi (“Hitung”, “Selisih”).
- JCalendar: Pemilih tanggal visual.

---

## ⚙️ Kode Program Utama

### 🔹 Konstruktor Form
Inisialisasi awal ComboBox bulan, Spinner tahun, dan label hasil.

```java
public FormPerhitunganHari() {
    initComponents();
    setResizable(false);
    setLocationRelativeTo(null);
    String[] bulan = {"Januari", "Februari", "Maret", "April", "Mei", "Juni",
                      "Juli", "Agustus", "September", "Oktober", "November", "Desember"};
    cmbBulan.removeAllItems();
    for (String b : bulan) {
        cmbBulan.addItem(b);
    }

    javax.swing.SpinnerNumberModel modelTahun = new javax.swing.SpinnerNumberModel(2024, 1, 3000, 1);
    spnTahun.setModel(modelTahun);
    javax.swing.JSpinner.NumberEditor editor = new javax.swing.JSpinner.NumberEditor(spnTahun, "#");
    spnTahun.setEditor(editor);
    lblHasil.setText("Jumlah Hari: -");
    lblInfoHari.setText("Hari pertama & terakhir: -");

}
```

---

### 🔹 Tombol Hitung Jumlah Hari
Menghitung jumlah hari dalam bulan dan menampilkan hari pertama serta terakhir.

```java
private void btnHitungActionPerformed(java.awt.event.ActionEvent evt) {
    try {
        int tahun = (Integer) spnTahun.getValue();
        int bulanIndex = cmbBulan.getSelectedIndex() + 1; 
        LocalDate tanggal = LocalDate.of(tahun, bulanIndex, 1);

        int jumlahHari = tanggal.lengthOfMonth();
        LocalDate hariPertama = tanggal.withDayOfMonth(1);
        LocalDate hariTerakhir = tanggal.withDayOfMonth(jumlahHari);

       lblHasil.setText("Jumlah Hari: " + jumlahHari + " hari");
        lblInfoHari.setText("Hari pertama: " 
                + hariPertama.getDayOfWeek().getDisplayName(TextStyle.FULL, new Locale("id", "ID"))
                + " | Hari terakhir: " 
                + hariTerakhir.getDayOfWeek().getDisplayName(TextStyle.FULL, new Locale("id", "ID")));


        lblHasil.setText("Jumlah Hari: " + jumlahHari);
        lblInfoHari.setText("Hari pertama: " + hariPertama + ", Hari terakhir: " + hariTerakhir);
    } catch (Exception e) {
        JOptionPane.showMessageDialog(this, "Input tidak valid!", "Error", JOptionPane.ERROR_MESSAGE);
    }
}
```

---

### 🔹 Tombol Hitung Selisih Hari
Menghitung selisih hari antara dua tanggal di `JCalendar2` dan `JCalendar3`.

```java
private void btnSelisihActionPerformed(java.awt.event.ActionEvent evt) {
    try {
        java.util.Date d1 = jCalendar2.getDate();
        java.util.Date d2 = jCalendar3.getDate();

        if (d1 == null || d2 == null) {
            JOptionPane.showMessageDialog(this, "Pastikan kedua kalender sudah memiliki tanggal terpilih!");
            return;
        }

        java.time.LocalDate tanggal1 = d1.toInstant().atZone(java.time.ZoneId.systemDefault()).toLocalDate();
        java.time.LocalDate tanggal2 = d2.toInstant().atZone(java.time.ZoneId.systemDefault()).toLocalDate();

        // Hitung selisih hari
        long selisih = Math.abs(java.time.temporal.ChronoUnit.DAYS.between(tanggal1, tanggal2));

        // Tampilkan hasil
        JOptionPane.showMessageDialog(this,
            "Selisih antara " + tanggal1 + " dan " + tanggal2 + " adalah " + selisih + " hari.",
            "Hasil Selisih", JOptionPane.INFORMATION_MESSAGE);

    } catch (Exception e) {
        JOptionPane.showMessageDialog(this,
            "Terjadi kesalahan saat menghitung selisih: " + e.getMessage(),
            "Error", JOptionPane.ERROR_MESSAGE);
    }

}                   
```

---

### 🔹 Update Kalender Berdasarkan Input Bulan & Tahun
Sinkronisasi input bulan dan tahun ke tampilan kalender.

```java
private void updateCalendar() {
    try {
        int bulan = cmbBulan.getSelectedIndex();
        Object tahunObj = spnTahun.getValue();
        int tahun;

        if (tahunObj instanceof Number) {
            tahun = ((Number) tahunObj).intValue();
        } else {
            tahun = Integer.parseInt(tahunObj.toString().replace(".", ""));
        }

        if (tahun < 1 || tahun > 3000) {
            return;
        }

        java.util.Calendar cal = java.util.Calendar.getInstance();
        java.util.Date cur = jCalendar2.getDate();

        if (cur != null) {
            cal.setTime(cur);
        } else {
            cal.set(java.util.Calendar.DAY_OF_MONTH, 1);
        }

       cal.set(java.util.Calendar.YEAR, tahun);
        cal.set(java.util.Calendar.MONTH, bulan);

        // Pastikan hari tidak melebihi jumlah hari pada bulan baru
        int maxDay = cal.getActualMaximum(java.util.Calendar.DAY_OF_MONTH);
        int day = Math.min(cal.get(java.util.Calendar.DAY_OF_MONTH), maxDay);
        cal.set(java.util.Calendar.DAY_OF_MONTH, day);

        // Update kalender supaya sinkron
        jCalendar2.setDate(cal.getTime());

    } catch (Exception e) {
        // Jangan munculkan pesan pop-up saat awal program, cukup diam
        System.err.println("Gagal memperbarui kalender: " + e.getMessage());
    }
}
```

---

### 🔹 Sinkronisasi Kalender dengan Input
Ketika pengguna mengubah bulan/tahun, `JCalendar2` ikut berubah otomatis.

```java
private void spnTahunStateChanged(javax.swing.event.ChangeEvent evt) {
    lblHasil.setText("Jumlah Hari: -");
    lblInfoHari.setText("Hari pertama & terakhir: -");
    updateCalendar();
    }                 

private void cmbBulanActionPerformed(java.awt.event.ActionEvent evt) {
    updateCalendar();
}
```

---

### 🔹 Event Sinkronisasi dari Kalender ke Input
Ketika tanggal di `JCalendar2` diubah, ComboBox dan Spinner ikut menyesuaikan.

```java
private void jCalendar2PropertyChange(java.beans.PropertyChangeEvent evt) {
    if ("calendar".equals(evt.getPropertyName()) && jCalendar2.getDate() != null) {
        // Jalankan setelah UI selesai update agar jCalendar.getDate() konsisten
        javax.swing.SwingUtilities.invokeLater(() -> {
            try {
                java.util.Date dateNow = jCalendar2.getDate();
                if (dateNow != null) {
                    java.util.Calendar cal = java.util.Calendar.getInstance();
                    cal.setTime(dateNow);

                    // pastikan cmbBulan & spnTahun sinkron dengan tampilan kalender
                    cmbBulan.setSelectedIndex(cal.get(java.util.Calendar.MONTH));
                    spnTahun.setValue(cal.get(java.util.Calendar.YEAR));
                }
            } catch (Exception ex) {
                // jangan crash; log/notify singkat
                System.err.println("Error propertyChange jCalendar2: " + ex.getMessage());
            }
        });
    }
    }
```

---
## 🐾 Menghitung hari

<img width="1597" height="723" alt="image" src="https://github.com/user-attachments/assets/f58bad0f-794e-482d-803f-e4943895b7cc" />



---

---

## 🍁 Hitung Selisih

<img width="1587" height="799" alt="image" src="https://github.com/user-attachments/assets/9c728980-eded-42e8-8d3c-02e9478fa8b7" />


## 🧾 Indikator Penilaian

| No | Komponen | Persentase | Keterangan |
|----|-----------|-------------|-------------|
| 1 | Komponen GUI | 10% | Semua elemen GUI sesuai instruksi |
| 2 | Logika Program | 20% | Menggunakan LocalDate dan ChronoUnit |
| 3 | Events | 20% | Semua event berfungsi dengan benar |
| 4 | Kesesuaian UI | 20% | Tampilan rapi dan mudah digunakan |
| 5 | Variasi | 30% | Ada selisih hari, hari pertama & terakhir |
| **Total** | **100%** | ✅ Lengkap dan berfungsi penuh |

---

## 👨‍💻 Pembuat
**Nama:** Al-Fadilah Nur Sahdan Al-Biya  
**NPM:** 2310010465   
**Proyek:** Tugas 4 – Aplikasi Perhitungan Hari  
