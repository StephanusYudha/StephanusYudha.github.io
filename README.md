# StephanusYudha.github.io
<!DOCTYPE html>
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
        .img-table { width: 50px; height: 50px; object-fit: cover; border-radius: 6px; }
        .rekap-box { background: white; border-radius: 10px; padding: 15px; margin-bottom: 20px; border-left: 5px solid var(--bkk-gold); }
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
        <p class="text-muted small mt-3">Gunakan password standar kantor</p>
    </div>
</div>

<div class="header-bank">
    <img src="https://bankbanjarharjo.id/assets/upload/images/logo.png" class="logo-bank">
    <div>
        <h4 class="fw-bold mb-0">BPR BKK JATENG (PERSERODA)</h4>
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
                    <select id="statusKunjungan" class="form-select mb-3" required>
                        <option value="" selected disabled>Pilih Status...</option>
                        <option value="Janji Bayar">Janji Bayar</option>
                        <option value="Bayar Sebagian">Bayar Sebagian</option>
                        <option value="Titip Angsuran">Titip Angsuran</option>
                        <option value="Tidak Bertemu">Tidak Bertemu</option>
                        <option value="Rumah Kosong">Rumah Kosong</option>
                    </select>
                    <textarea id="hasilKunjungan" class="form-control mb-2" rows="2" placeholder="Hasil Lapangan..." required></textarea>
                    <textarea id="detailSolusi" class="form-control mb-3" rows="2" placeholder="Solusi/Rencana..." required></textarea>
                    <label class="small fw-bold text-danger">FOTO BUKTI</label>
                    <input type="file" id="fotoKunjungan" class="form-control form-control-sm mb-2" accept="image/*" capture="camera" required>
                    <img id="preview-foto" class="mx-auto d-block">
                    <input type="hidden" id="lokasiGps">
                    <button type="submit" class="btn btn-primary btn-lg w-100 fw-bold mt-3 shadow">SIMPAN DATA</button>
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
                    <table class="table table-sm table-hover align-middle border" id="tabelLaporan">
                        <thead class="table-dark small">
                            <tr>
                                <th>Foto</th>
                                <th>Nasabah</th>
                                <th>Petugas</th>
                                <th>Status</th>
                                <th>Detail</th>
                                <th>Peta</th>
                            </tr>
                        </thead>
                        <tbody id="tbodyLaporan" class="small"></tbody>
                    </table>
                </div>
                <button onclick="hapusSemuaData()" class="btn btn-link btn-sm text-danger mt-3 text-decoration-none">Hapus Data Hari Ini</button>
            </div>
        </div>
    </div>
</div>

<script>
    const MASTER_PASS = "bkk123"; // PASSWORD ANDA
    let dataLaporan = JSON.parse(localStorage.getItem('laporan_bkk')) || [];
    let petugas = localStorage.getItem('petugas_aktif') || "";
    let base64Foto = "";

    window.onload = () => { if(petugas) loginSukses(); getGPS(); tampilkanData(); };

    function prosesLogin() {
        const n = document.getElementById('userKolektor').value;
        const p = document.getElementById('passKolektor').value;
        if (n && p === MASTER_PASS) {
            localStorage.setItem('petugas_aktif', n);
            petugas = n;
            loginSukses();
        } else { alert("Nama atau Password Salah!"); }
    }

    function loginSukses() {
        document.getElementById('loginOverlay').style.display = 'none';
        document.getElementById('labelPetugas').innerText = "Petugas: " + petugas;
    }

    function logout() { localStorage.removeItem('petugas_aktif'); location.reload(); }

    function getGPS() {
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(pos => {
                document.getElementById('lokasiGps').value = pos.coords.latitude + "," + pos.coords.longitude;
            });
        }
    }

    document.getElementById('fotoKunjungan').addEventListener('change', function() {
        const reader = new FileReader();
        reader.onload = (e) => { 
            base64Foto = e.target.result; 
            document.getElementById('preview-foto').src = base64Foto;
            document.getElementById('preview-foto').style.display = 'block';
        };
        reader.readAsDataURL(this.files[0]);
    });

    document.getElementById('collectionForm').addEventListener('submit', function(e) {
        e.preventDefault();
        const entri = {
            petugas: petugas,
            foto: base64Foto,
            nama: document.getElementById('namaNasabah').value,
            waktu: new Date().toLocaleTimeString('id-ID', {hour:'2-digit', minute:'2-digit'}),
            status: document.getElementById('statusKunjungan').value,
            solusi: document.getElementById('detailSolusi').value,
            hasil: document.getElementById('hasilKunjungan').value,
            gps: document.getElementById('lokasiGps').value
        };
        dataLaporan.unshift(entri);
        localStorage.setItem('laporan_bkk', JSON.stringify(dataLaporan));
        this.reset();
        document.getElementById('preview-foto').style.display = 'none';
        tampilkanData();
        alert("Data Berhasil Disimpan!");
    });

    function tampilkanData() {
        const table = document.getElementById('tbodyLaporan');
        const keyword = document.getElementById('cariNasabah').value.toLowerCase();
        table.innerHTML = "";
        
        let cBayar = 0;
        dataLaporan.forEach(item => {
            if(item.status.includes("Angsuran") || item.status.includes("Sebagian")) cBayar++;
            if (!item.nama.toLowerCase().includes(keyword)) return;

            const row = table.insertRow();
            let color = "bg-info text-dark";
            if (item.status === "Janji Bayar") color = "bg-warning text-dark";
            if (item.status.includes("Tidak") || item.status.includes("Kosong")) color = "bg-danger";
            if (item.status.includes("Angsuran") || item.status.includes("Sebagian")) color = "bg-success";

            row.insertCell(0).innerHTML = `<img src="${item.foto}" class="img-table" onclick="window.open(this.src)">`;
            row.insertCell(1).innerHTML = `<strong>${item.nama}</strong><br><small class="text-muted">${item.waktu}</small>`;
            row.insertCell(2).innerText = item.petugas;
            row.insertCell(3).innerHTML = `<span class="badge ${color}">${item.status}</span>`;
            row.insertCell(4).innerHTML = `<small><b>Hasil:</b> ${item.hasil}<br><b>Solusi:</b> ${item.solusi}</small>`;
            row.insertCell(5).innerHTML = `<a href="https://www.google.com/maps?q=${item.gps}" target="_blank" class="btn btn-sm btn-outline-primary"><i class="bi bi-geo-alt"></i></a>`;
        });

        document.getElementById('countTotal').innerText = dataLaporan.length;
        document.getElementById('countBayar').innerText = cBayar;
        document.getElementById('countPending').innerText = dataLaporan.length - cBayar;
    }

    function hapusSemuaData() { if(confirm("Hapus semua data?")) { localStorage.removeItem('laporan_bkk'); dataLaporan = []; tampilkanData(); } }

    function exportToExcel() {
        const wb = XLSX.utils.table_to_book(document.getElementById("tabelLaporan"), {sheet: "Monitoring"});
        XLSX.writeFile(wb, "Monitoring_BKK_" + petugas + ".xlsx");
    }
</script>
</body>
</html>
