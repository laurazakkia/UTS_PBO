# Konsep Pemrograman Berorientasi Objek - UTS

---

## Langkah - langkah : 
### 1. Membuat Database baru pada postgreSQL
### 2. Membuat tabel beserta atributnya pada postgreSQL
### 3. Membuat project baru pada Netbeans
### 4. Mengoneksikan postgreSQL dengan java menggunakan JDBC
  <pre>
    Connection conn;
    Statement stmt;
    PreparedStatement pstmt = null;

    String driver = "org.postgresql.Driver";
    String koneksi = "jdbc:postgresql://localhost:5432/UTS_PBO";
    String user = "postgres";
    String password = "Laura12345";
    InputStreamReader inputStreamReader = new InputStreamReader(System.in);
    BufferedReader input = new BufferedReader(inputStreamReader);
  </pre>

### 5. Membuat User Interface dengan Java Swing
  Menggunakan beberapa fitur pada Java Swing :
  - Label yang akan digunakan untuk membuat teks (KodeMK, SKS, NamaMk, SemesterAjar)
  - Text Field untuk membuat field data (tfKodeMK, tfSks, tfNama, tfSmt)
  - Button untuk membuat tombol pengoperasian CRUD (btnTambah, btnUpdate, btnHapus)
  - Table untuk membuat tabel yang digunakan untuk menampilkan data (Tabel)

### 6. Metode untuk menampilkan tabel
   <pre>
     public void showTable() {
        try (Connection conn = DriverManager.getConnection(koneksi, user, password); Statement stmt = conn.createStatement(); ResultSet rs = stmt.executeQuery("SELECT * FROM mataKuliah ORDER BY kode_mk")) {

            // Dapatkan model tabel yang ada
            DefaultTableModel model = (DefaultTableModel) tabel.getModel();

            // Kosongkan data tabel terlebih dahulu
            model.setRowCount(0);

            // Tambahkan data dari ResultSet ke model tabel
            while (rs.next()) {
                Object[] rowData = {rs.getString(1), rs.getString(2), rs.getString(3), rs.getString(4)};
                model.addRow(rowData);
            }
        } catch (SQLException ex) {
            // Tangani eksepsi
            System.err.println("Terjadi kesalahan: " + ex.getMessage());
        }
    }
   </pre>

### 7. Penggunaan btnTambah
   Pada Button Tambah (btnTambah), berfungsi untuk menambah isi data pada database. 
   <pre>
     private void btnTambahActionPerformed(java.awt.event.ActionEvent evt) {                                          
        // TODO add your handling code here:
        if (tfKode.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (tfSks.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (tfNama.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (tfSmt.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else {
            try {
                Class.forName(driver);
                conn = DriverManager.getConnection(koneksi, user, password);
                conn.setAutoCommit(false);

                String sql = "INSERT INTO mataKuliah VALUES(?,?,?,?)";
                pstmt = conn.prepareStatement(sql);

                String kode_mk, sks, nama_mk, semester_ajar;
                kode_mk = tfKode.getText();
                sks = tfSks.getText();
                nama_mk = tfNama.getText();
                semester_ajar = tfSmt.getText();

                pstmt.setString(1, kode_mk);
                pstmt.setString(2, sks);
                pstmt.setString(3, nama_mk);
                pstmt.setString(4, semester_ajar);

                pstmt.executeUpdate();
                conn.commit();
                pstmt.close();
                conn.close();

                JOptionPane.showMessageDialog(null, "Sukses diinput");
            } catch (ClassNotFoundException | SQLException ex) {
                JOptionPane.showMessageDialog(null, "Terjadi kesalahan saat pengisian");
            }
        }
        showTable();
    }           
   </pre>
### 8. Penggunaan btnUpdate
Pada Button Update (btnUpdate), berfungsi untuk merubah isi data yang ada dalam database.
<pre>
  private void btnUpdateActionPerformed(java.awt.event.ActionEvent evt) {                                          
        // TODO add your handling code here:
        String kode_mk, sks, nama_mk, semester_ajar;
         if (tfKode.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (tfSks.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (tfNama.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (tfSmt.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else {

            try {
                Class.forName(driver);
                String sql = "UPDATE mataKuliah SET sks = ?, nama_mk = ?, semester_ajar = ? WHERE kode_mk = ?";
                conn = DriverManager.getConnection(koneksi, user, password);
                pstmt = conn.prepareStatement(sql);

                kode_mk = tfKode.getText();
                sks = tfSks.getText();
                nama_mk = tfNama.getText();
                semester_ajar = tfSmt.getText();

                pstmt.setString(1, sks);
                pstmt.setString(2, nama_mk);
                pstmt.setString(3, semester_ajar);
                pstmt.setString(4, kode_mk);

                int rowsAffected = pstmt.executeUpdate();
                if (rowsAffected > 0) {
                    JOptionPane.showMessageDialog(null, "Data berhasil diupdate");
                    pstmt.close();
                    conn.close();
                    bersih();
                } else {
                    JOptionPane.showMessageDialog(null, "Data tidak ditemukan");
                }
            } catch (ClassNotFoundException | SQLException ex) {
                Logger.getLogger(mataKuliah.class.getName()).log(Level.SEVERE, null, ex);

            }
        }
        showTable();
    }                  
</pre>

### 9. Penggunaan btnHapus
Pada Button Hapus (btnHapus), berfungsi untuk menghapus isi data yang ada dalam database.  
<pre>
  private void btnHapusActionPerformed(java.awt.event.ActionEvent evt) {                                         
        // TODO add your handling code here:
        String kode_mk;
        kode_mk = tfKode.getText();

        try {
            Class.forName(driver);
            conn = DriverManager.getConnection(koneksi, user, password);

            int jawab = JOptionPane.showConfirmDialog(null, "Silakan Konfirmasi?");
            switch (jawab) {
                case JOptionPane.YES_OPTION:
                    String deleteSql = "DELETE FROM mataKuliah WHERE kode_mk = ?";
                    pstmt = conn.prepareStatement(deleteSql);
                    pstmt.setString(1, kode_mk);
                    pstmt.executeUpdate();
                    pstmt.close();
                    conn.close();
                    bersih();
                    break;
                case JOptionPane.NO_OPTION:
                    JOptionPane.showMessageDialog(this, "Kamu menjawab tidak");
                    break;
            }
        } catch (ClassNotFoundException | SQLException ex) {
            JOptionPane.showConfirmDialog(null, "Cek Lagi!!!");
        }
        showTable();
    }            
</pre>

## Penerapan
### 1. Menambah data
Isi data yang akan ditambahkan pada TextField.

![{EAC33427-0CFA-4CFC-B89F-2A19B0DC8A70}](https://github.com/user-attachments/assets/74dc61c1-167e-405c-b08a-c826a4d1493f)

Klik **Tambah**, dan data akan otomatis ada pada tabel

![{90DB4C49-331E-4CCD-AE86-BD83595DD5AA}](https://github.com/user-attachments/assets/714f05ab-f518-4b1b-9ce4-8268053de585)

### 2. Update data
Pilih data yang akan diupdate lalu update sesuai keinginan

![{7E400B64-5E43-43A6-B3E2-5C5AC37611A7}](https://github.com/user-attachments/assets/9a26a9f4-e411-4ce6-9dbd-80e1086ba548)

Klik **Update**, dan data akan otomatis terupdate

![{48378FE6-C6CA-4F4E-A443-2F12BF0C84E2}](https://github.com/user-attachments/assets/2999e5df-b218-446f-a972-af41809d36c8)

### 3. Hapus Data
Pilih data yang akan dihapus.

![{99CA084C-3A63-418F-B7D6-0E5FB3F32EE7}](https://github.com/user-attachments/assets/d81570b8-f7a3-44a4-88ef-d1f7db2e55a5)

Klik **Hapus**, dan data akan terhapus.

![{971620DD-42ED-43DC-88CC-D66429CDDB44}](https://github.com/user-attachments/assets/1f28abb6-4698-4f4b-8eaa-18ef68a0d75e)

