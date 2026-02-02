<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Monitoring Collection - BPR BKK Jateng</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.1/font/bootstrap-icons.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        :root { --bkk-blue: #004a99; --bkk-gold: #ffcc00; }
        body { background-color: #f0f2f5; font-family: 'Segoe UI', sans-serif; }
        .header-bank { background: var(--bkk-blue); color: white; padding: 20px; border-bottom: 5px solid var(--bkk-gold); margin-bottom: 25px; display: flex; align-items: center; justify-content: center; gap: 20px; position: relative; }
        .logo-bank { width: 65px; background: white; padding: 5px; border-radius: 10px; }
        #loginOverlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,74,153,0.98); z-index: 9999; display: flex; align-items: center; justify-content: center; }
        .card { border: none; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.05); }
        #preview-foto { max-width: 100%; height: 150px; object-fit: cover; display: none; border-radius: 8px; margin-top: 10px; border: 2px solid #ddd; }
        .img-table { width: 50px; height: 50px; object-fit: cover; border-radius: 6px; cursor: pointer; }
        .rekap-box { background: white; border-radius: 10px; padding: 15px; margin-bottom: 20px; border-left: 5px solid var(--bkk-gold); }
        .status-sync { font-size: 0.7rem; margin-top: 5px; }
    </style>
</head>
<body>

<div id="loginOverlay">
    <div class="card p-4 text-center shadow-lg" style="max-width: 350px; width: 90%;">
        <img src="https://bankbanjarharjo.id/assets/upload/images/logo.png" width="70" class="mx-auto mb-3">
        <h4 class="fw-bold">Akses Kolektor</h4>
        <input type="text" id="userKolektor" class="form-control mb-2 text-center" placeholder="Nama Lengkap">
        <input type="password" id="passKolektor" class="form-control mb-3 text-center" placeholder="Password Sistem">
        <button onclick="prosesLogin()" class="btn btn-primary w-100 fw-bold">LOGIN</button>
    </div>
</div>

<div class="header-bank">
    <img src="https://bankbanjarharjo.id/assets/upload/images/logo.png" class="logo-bank">
    <div>
        <h4 class="fw-bold mb-0 text-center">BPR BKK JATENG</h4>
        <span id="labelPetugas" class="badge bg-warning text-dark">Petugas: -</span>
    </div>
    <button onclick="logout()" class="btn btn-sm btn-outline-light position-absolute top-0 end-0 m-2"><i class="bi bi-power"></i></button>
</div>

<div class="container-fluid px-4">
    <div class="row">
        <div class="col-lg-4">
            <div class="card p-4 mb-4">
                <h5 class="fw-bold text-primary mb-3">Laporan Kunjungan</h5>
                <form id="collectionForm">
                    <input type="text" id="namaNasabah" class="form-control mb-3" placeholder="Nama Nasabah" required>
                    
                    <select id="bertemuDengan" class="form-select mb-3" required>
                        <option value="" selected disabled>Bertemu Dengan...</option>
                        <option value="Debitur">Debitur</option>
                        <option value="Suami / Istri">Suami / Istri</option>
                        <option value="Keluarga">Keluarga Lain</option>
                        <option value="Tidak Bertemu">Tidak Ada Orang</option>
                    </select> 
                  
                    <select id="statusKunjungan" class="form-select mb-3" required>
                        <option value="" selected disabled>Status Penagihan...</option>
                        <option value="Janji Bayar">Janji Bayar</option>
                        <option value="Bayar Sebagian">Bayar Sebagian</option>
                        <option value="Titip Angsuran">Titip Angsuran</option>
                        <option value="Rumah Kosong">Rumah Kosong</option>
                    </select>

                    <textarea id="hasilKunjungan" class="form-control mb-2" rows="2" placeholder="Hasil Lapangan..." required></textarea>
                    <textarea id="detailSolusi" class="form-control mb-3" rows="2" placeholder="Solusi/Rencana..." required></textarea>
                    
                    <label class="small fw-bold text-danger">FOTO BUKTI</label>
                    <input type="file" id="fotoKunjungan" class="form-control form-control-sm mb-2" accept="image/*" capture="camera" required>
                    <img id="preview-foto" class="mx-auto d-block">
                    
                    <input type="hidden" id="lokasiGps">
                    <button type="submit" id="btnSimpan" class="btn btn-primary btn-lg w-100 fw-bold mt-3 shadow">SIMPAN & SINKRON</button>
                    <div id="syncStatus" class="text-center status-sync text-muted"></div>
                </form>
            </div>
        </div>

        <div class="col-lg-8">
            <div class="rekap-box d-flex justify-content-around text-center shadow-sm">
                <div><small class="text-muted">Total Kunjungan</small><h4 id="countTotal" class="fw-bold mb-0">0</h4></div>
                <div class="text-success"><small class="text-muted">Tertagih</small><h4 id="countBayar" class="fw-bold mb-0">0</h4></div>
                <div class="text-danger"><small class="text-muted">Pending</small><h4 id="countPending" class="fw-bold mb-0">0</h4></div>
            </div>

            <div class="card p-4">
                <div class="d-flex justify-content-between mb-3">
                    <input type="text" id="cariNasabah" class="form-control w-50 form-control-sm" placeholder="Cari Nasabah..." onkeyup="tampilkanData()">
                    <button onclick="exportToExcel()" class="btn btn-success btn-sm px-3 fw-bold">DOWNLOAD EXCEL</button>
                </div>
                <div class="table-responsive">
                    <table class="table table-sm table-hover align-middle border">
                  <thead class="table-dark small">
    <tr>
        <th>Foto</th>
        <th>Nasabah</th>
        <th>Petugas</th>
        <th>Status</th>
        <th>Detail</th>
        <th>Peta</th>
        <th>Aksi</th> </tr>
</thead>
                        <tbody id="tbodyLaporan" class="small"></tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
    const MASTER_PASS = "bkk123";
    // GANTI INI DENGAN URL APPS SCRIPT ANDA
    const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbxdLJdJ3bz6rbwh3IYkK_Ra3N3UbY-KNWYpILEnrg4p4z-pROLaLTie2z9iLUUASQz5/exec"; 
    
    let dataLaporan = JSON.parse(localStorage.getItem('laporan_bkk')) || [];
    let petugas = localStorage.getItem('petugas_aktif') || "";
    let base64Foto = "";

    window.onload = () => { 
        if(petugas) loginSukses(); 
        getGPS(); 
        tampilkanData(); 
    };

    function prosesLogin() {
        const n = document.getElementById('userKolektor').value;
        const p = document.getElementById('passKolektor').value;
        if (n && p === MASTER_PASS) {
            localStorage.setItem('petugas_aktif', n);
            petugas = n;
            loginSukses();
        } else { alert("Akses Ditolak!"); }
    }

    function loginSukses() {
        document.getElementById('loginOverlay').style.display = 'none';
        document.getElementById('labelPetugas').innerText = "Petugas: " + petugas;
    }

    function logout() { localStorage.removeItem('petugas_aktif'); location.reload(); }

    function getGPS() {
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(
                pos => { document.getElementById('lokasiGps').value = `${pos.coords.latitude},${pos.coords.longitude}`; },
                err => { console.warn("GPS Gagal dimuat"); }
            );
        }
    }

    // Fungsi Kompresi Gambar
    function compressImage(file, callback) {
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = event => {
            const img = new Image();
            img.src = event.target.result;
            img.onload = () => {
                const canvas = document.createElement('canvas');
                const MAX_WIDTH = 600; 
                const scaleSize = MAX_WIDTH / img.width;
                canvas.width = MAX_WIDTH;
                canvas.height = img.height * scaleSize;
                const ctx = canvas.getContext('2d');
                ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
                callback(canvas.toDataURL('image/jpeg', 0.7));
            };
        };
    }

    document.getElementById('fotoKunjungan').addEventListener('change', function() {
        if (this.files[0]) {
            compressImage(this.files[0], (compressed) => {
                base64Foto = compressed;
                const preview = document.getElementById('preview-foto');
                preview.src = base64Foto;
                preview.style.display = 'block';
            });
        }
    });

    document.getElementById('collectionForm').addEventListener('submit', async function(e) {
        e.preventDefault();
        const btn = document.getElementById('btnSimpan');
        const syncLabel = document.getElementById('syncStatus');
        
        const entri = {
            petugas: petugas,
            waktu: new Date().toLocaleString('id-ID'),
            nama: document.getElementById('namaNasabah').value,
            bertemu: document.getElementById('bertemuDengan').value,
            status: document.getElementById('statusKunjungan').value,
            hasil: document.getElementById('hasilKunjungan').value,
            solusi: document.getElementById('detailSolusi').value,
            gps: document.getElementById('lokasiGps').value || "0,0",
            foto: base64Foto
        };

        dataLaporan.unshift(entri);
        localStorage.setItem('laporan_bkk', JSON.stringify(dataLaporan));
        tampilkanData();
        
        btn.disabled = true;
        btn.innerText = "MENYINKRONKAN...";
        syncLabel.innerHTML = "Sedang mengirim ke cloud...";

        try {
            await fetch(SCRIPT_URL, {
                method: 'POST',
                mode: 'no-cors',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(entri)
            });
            syncLabel.innerHTML = "<span class='text-success'>✓ Berhasil Sinkron</span>";
        } catch (err) {
            syncLabel.innerHTML = "<span class='text-danger'>! Gagal Sinkron (Data Aman di Lokal)</span>";
        }

        this.reset();
        base64Foto = "";
        document.getElementById('preview-foto').style.display = 'none';
        btn.disabled = false;
        btn.innerText = "SIMPAN & SINKRON";
        alert("Data Kunjungan Berhasil Dicatat!");
    });

    function tampilkanData() {
    const table = document.getElementById('tbodyLaporan');
    const search = document.getElementById('cariNasabah').value.toLowerCase();
    table.innerHTML = "";
    let cBayar = 0;

    dataLaporan.forEach((item, index) => {
        if (!item.nama.toLowerCase().includes(search)) return;

        const isBayar = item.status.toLowerCase().match(/bayar|titip/);
        if(isBayar) cBayar++;
        
        const row = table.insertRow();
        
        // Isi data tiap kolom
        row.insertCell(0).innerHTML = `<img src="${item.foto || 'https://via.placeholder.com/50'}" class="img-table" onclick="window.open(this.src)">`;
        row.insertCell(1).innerHTML = `<strong>${item.nama}</strong><br><small>${item.waktu}</small>`;
        row.insertCell(2).innerText = item.petugas;
        row.insertCell(3).innerHTML = `<span class="badge ${isBayar?'bg-success':'bg-warning text-dark'}">${item.status}</span>`;
        row.insertCell(4).innerHTML = `<small>${item.hasil}</small>`;
        row.insertCell(5).innerHTML = `<a href="https://www.google.com/maps?q=${item.gps}" target="_blank" class="btn btn-sm btn-outline-primary"><i class="bi bi-geo-alt"></i></a>`;
        
        // KOLOM HAPUS (Pastikan baris ini ada)
        row.insertCell(6).innerHTML = `
            <button onclick="hapusData(${index})" class="btn btn-sm btn-danger">
                <i class="bi bi-trash"></i>
            </button>`;
    });

    // Update Angka Rekap
    document.getElementById('countTotal').innerText = dataLaporan.length;
    document.getElementById('countBayar').innerText = cBayar;
    document.getElementById('countPending').innerText = dataLaporan.length - cBayar;
}

    function exportToExcel() {
        if(dataLaporan.length === 0) return alert("Data kosong!");
        const ws = XLSX.utils.json_to_sheet(dataLaporan.map(i => ({...i, foto: 'Lihat di Sistem'})));
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "Laporan");
        XLSX.writeFile(wb, `Laporan_BKK_${petugas}.xlsx`);
    }
  function hapusData(index) {
    if (confirm("Apakah Anda yakin ingin menghapus data ini dari perangkat? \n(Catatan: Data yang sudah tersinkron ke Google Sheets tidak akan terhapus)")) {
        // Hapus dari array berdasarkan index
        dataLaporan.splice(index, 1);
        
        // Update LocalStorage
        localStorage.setItem('laporan_bkk', JSON.stringify(dataLaporan));
        
        // Refresh tabel
        tampilkanData();
    }
}
</script>
</body>
</html>
