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
        .btn-xs { padding: 0.2rem 0.4rem; font-size: 0.7rem; }
        .pulse { animation: pulse-animation 2s infinite; }
        @keyframes pulse-animation { 0% { opacity: 1; } 50% { opacity: 0.5; } 100% { opacity: 1; } }
        .sticky-top { top: -1px; z-index: 10; }
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

<div class="container-fluid">
    <div class="card p-3 mb-4 shadow-sm border-success">
        <div class="d-flex justify-content-between align-items-center mb-3">
            <h6 class="fw-bold text-success mb-0"><i class="bi bi-database-check"></i> Data Master Rencana</h6>
            <div id="importStatus" class="small fw-bold"></div>
        </div>

        <div class="row g-2 mb-3 align-items-end">
            <div class="col-md-3">
                <label class="small fw-bold text-muted">Import Excel:</label>
                <input type="file" id="importExcel" class="form-control form-control-sm border-success" accept=".xlsx, .xls">
            </div>
            <div class="col-md-3">
                <label class="small fw-bold text-muted">Cari Nama/Alamat:</label>
                <input type="text" id="searchMaster" class="form-control form-control-sm" placeholder="Ketik nama..." onkeyup="tampilkanMasterExcel()">
            </div>
            <div class="col-md-3">
                <label class="small fw-bold text-muted">Filter Tgl Master:</label>
                <input type="date" id="filterTglMaster" class="form-control form-control-sm" onchange="tampilkanMasterExcel()">
            </div>
            <div class="col-md-3 text-md-end">
                <button onclick="hapusSemuaRencana()" class="btn btn-sm btn-outline-danger fw-bold w-100">
                    <i class="bi bi-trash"></i> KOSONGKAN DATA
                </button>
            </div>
        </div>

        <div class="table-responsive" style="max-height: 250px;">
            <table class="table table-sm table-striped border">
                <thead class="table-success small text-center sticky-top">
                    <tr>
                        <th>Nama</th>
                        <th>Alamat</th>
                        <th>OS Nominal</th>
                        <th>No. Telp</th>
                        <th>Tagihan</th>
                        <th>Tanggal</th>
                    </tr>
                </thead>
                <tbody id="tbodyMasterExcel" class="small">
                    <tr><td colspan="6" class="text-center text-muted py-3">Tidak ada data.</td></tr>
                </tbody>
            </table>
        </div>
    </div>

    <div class="row">
        <div class="col-lg-4">
            <div class="card p-3 mb-4 bg-light border-primary">
                <h6 class="fw-bold text-primary mb-3"><i class="bi bi-calendar-check"></i> Jadwal Kunjungan Hari Ini</h6>
                <input type="date" id="tglRencana" class="form-control form-control-sm mb-2" onchange="tampilkanTabelRencana()">
                <div class="table-responsive" style="max-height: 200px;">
                    <table class="table table-sm table-hover bg-white border">
                        <tbody id="tbodyRencana"></tbody>
                    </table>
                </div>
            </div>

            <div class="card p-4 mb-4">
                <h5 class="fw-bold text-primary mb-3">Laporan Lapangan</h5>
                <form id="collectionForm">
                    <input type="text" id="namaNasabah" class="form-control mb-3" placeholder="Nama Nasabah" required>
                    <select id="bertemuDengan" class="form-select mb-3" required>
                        <option value="" selected disabled>Bertemu Dengan...</option>
                        <option value="Debitur">Debitur</option>
                        <option value="Suami / Istri">Suami / Istri</option>
                        <option value="Keluarga">Keluarga</option>
                        <option value="Tidak Bertemu">Tidak Ada Orang</option>
                    </select>
                    <select id="statusKunjungan" class="form-select mb-3" required>
                        <option value="" selected disabled>Status Penagihan...</option>
                        <option value="Janji Bayar">Janji Bayar</option>
                        <option value="Bayar Sebagian">Bayar Sebagian</option>
                        <option value="Titip Angsuran">Titip Angsuran</option>
                        <option value="Rumah Kosong">Rumah Kosong</option>
                    </select>
                    <textarea id="hasilKunjungan" class="form-control mb-2" rows="2" placeholder="Hasil Kunjungan..." required></textarea>
                    <textarea id="detailSolusi" class="form-control mb-3" rows="2" placeholder="Rencana Tindak Lanjut..." required></textarea>
                    <input type="file" id="fotoKunjungan" class="form-control form-control-sm mb-2" accept="image/*" capture="camera" required>
                    <img id="preview-foto" class="mx-auto d-block mb-2">
                    <input type="hidden" id="lokasiGps">
                    <button type="submit" id="btnSimpan" class="btn btn-primary btn-lg w-100 fw-bold shadow">SIMPAN & SINKRON</button>
                    <div id="syncStatus" class="text-center mt-2 small"></div>
                </form>
            </div>
        </div>

        <div class="col-lg-8">
            <div class="rekap-box d-flex justify-content-around text-center shadow-sm">
                <div><small class="text-muted">Total</small><h4 id="countTotal" class="fw-bold mb-0">0</h4></div>
                <div class="text-success"><small class="text-muted">Tertagih</small><h4 id="countBayar" class="fw-bold mb-0">0</h4></div>
                <div class="text-danger"><small class="text-muted">Pending</small><h4 id="countPending" class="fw-bold mb-0">0</h4></div>
            </div>

            <div class="card p-4">
                <div class="d-flex justify-content-between mb-3 gap-2">
                    <input type="text" id="cariLaporan" class="form-control w-50 form-control-sm" placeholder="Cari laporan..." onkeyup="tampilkanData()">
                    <button onclick="exportToExcel()" class="btn btn-success btn-sm fw-bold">UNDUH EXCEL</button>
                </div>
                <div class="table-responsive">
                    <table class="table table-sm table-hover align-middle border">
                        <thead class="table-dark small">
                            <tr><th>Foto</th><th>Nasabah</th><th>Petugas</th><th>Status</th><th>Aksi</th></tr>
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
    const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbxdLJdJ3bz6rbwh3IYkK_Ra3N3UbY-KNWYpILEnrg4p4z-pROLaLTie2z9iLUUASQz5/exec";
    
    let dataLaporan = JSON.parse(localStorage.getItem('laporan_bkk')) || [];
    let petugas = localStorage.getItem('petugas_aktif') || "";
    let base64Foto = "";

    window.onload = () => {
        if(petugas) loginSukses();
        getGPS();
        tampilkanData();
        tampilkanMasterExcel();
        document.getElementById('tglRencana').value = new Date().toISOString().split('T')[0];
        tampilkanTabelRencana();
    };

    function prosesLogin() {
        const n = document.getElementById('userKolektor').value;
        const p = document.getElementById('passKolektor').value;
        if (n && p === MASTER_PASS) {
            localStorage.setItem('petugas_aktif', n);
            petugas = n; loginSukses();
        } else { alert("Password Salah!"); }
    }
    function loginSukses() { document.getElementById('loginOverlay').style.display = 'none'; document.getElementById('labelPetugas').innerText = "Petugas: " + petugas; }
    function logout() { localStorage.removeItem('petugas_aktif'); location.reload(); }
    function getGPS() { if (navigator.geolocation) { navigator.geolocation.getCurrentPosition(pos => { document.getElementById('lokasiGps').value = `${pos.coords.latitude},${pos.coords.longitude}`; }); } }

    // --- LOGIKA MASTER EXCEL DENGAN FILTER ---
    document.getElementById('importExcel').addEventListener('change', function(e) {
        const file = e.target.files[0];
        if(!file) return;
        const reader = new FileReader();
        const statusLabel = document.getElementById('importStatus');
        reader.onload = async (event) => {
            try {
                const data = new Uint8Array(event.target.result);
                const workbook = XLSX.read(data, { type: 'array', cellDates: true });
                const sheet = workbook.Sheets[workbook.SheetNames[0]];
                const rows = XLSX.utils.sheet_to_json(sheet, { header: 1 });
                const rencanaBaru = [];
                for (let i = 1; i < rows.length; i++) {
                    if (rows[i][0]) {
                        rencanaBaru.push({
                            nama: String(rows[i][0]).trim(),
                            alamat: rows[i][1] || '-',
                            os_nominal: rows[i][2] || 0,
                            telp: rows[i][3] || '-',
                            tagihan: rows[i][4] || 0,
                            tanggal: formatTanggalOtomatis(rows[i][5])
                        });
                    }
                }
                localStorage.setItem('rencana_kunjungan', JSON.stringify(rencanaBaru));
                tampilkanMasterExcel();
                tampilkanTabelRencana();
                statusLabel.innerHTML = `<span class="text-primary pulse">⏳ Sinkron Cloud...</span>`;
                await fetch(SCRIPT_URL, { method: 'POST', mode: 'no-cors', body: JSON.stringify({ type: "BATCH_IMPORT", data: rencanaBaru }) });
                statusLabel.innerHTML = `<span class="text-success">✅ Berhasil</span>`;
                e.target.value = "";
            } catch (error) { statusLabel.innerHTML = `<span class="text-danger">❌ Gagal</span>`; }
        };
        reader.readAsArrayBuffer(file);
    });

    function formatTanggalOtomatis(val) {
        if (!val) return "";
        if (val instanceof Date) return val.toISOString().split('T')[0];
        if (typeof val === 'number') return new Date((val - 25569) * 86400 * 1000).toISOString().split('T')[0];
        return String(val).trim();
    }

    function tampilkanMasterExcel() {
        const tbody = document.getElementById('tbodyMasterExcel');
        const search = document.getElementById('searchMaster').value.toLowerCase();
        const filterTgl = document.getElementById('filterTglMaster').value;
        const data = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
        
        const filtered = data.filter(r => {
            const matchText = r.nama.toLowerCase().includes(search) || r.alamat.toLowerCase().includes(search);
            const matchDate = filterTgl ? r.tanggal === filterTgl : true;
            return matchText && matchDate;
        });

        tbody.innerHTML = filtered.length ? "" : "<tr><td colspan='6' class='text-center text-muted'>Data tidak ditemukan</td></tr>";
        filtered.forEach(r => {
            const row = tbody.insertRow();
            row.innerHTML = `<td>${r.nama}</td><td>${r.alamat}</td><td class="text-end">${Number(r.os_nominal).toLocaleString('id-ID')}</td><td>${r.telp}</td><td class="text-end">${Number(r.tagihan).toLocaleString('id-ID')}</td><td class="text-center"><span class="badge bg-info text-dark">${r.tanggal}</span></td>`;
        });
    }

    function tampilkanTabelRencana() {
        const tgl = document.getElementById('tglRencana').value;
        const tbody = document.getElementById('tbodyRencana');
        const data = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
        tbody.innerHTML = "";
        const filtered = data.filter(r => r.tanggal === tgl);
        if(!filtered.length) { tbody.innerHTML = "<tr><td class='text-center py-3'>Kosong</td></tr>"; return; }
        filtered.forEach(r => {
            const row = tbody.insertRow();
            row.innerHTML = `<td class="d-flex justify-content-between align-items-center"><span><b>${r.nama}</b><br><small>${r.alamat}</small></span> <button class="btn btn-xs btn-primary" onclick="pilihNasabah('${r.nama}')">Pilih</button></td>`;
        });
    }

    function pilihNasabah(nama) {
        document.getElementById('namaNasabah').value = nama;
        window.scrollTo({ top: document.getElementById('collectionForm').offsetTop - 20, behavior: 'smooth' });
    }

    function hapusSemuaRencana() { if(confirm("Hapus semua data master?")) { localStorage.removeItem('rencana_kunjungan'); tampilkanMasterExcel(); tampilkanTabelRencana(); } }

    // --- FORM & LAPORAN ---
    function compressImage(file, callback) {
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = e => {
            const img = new Image(); img.src = e.target.result;
            img.onload = () => {
                const canvas = document.createElement('canvas');
                canvas.width = 600; canvas.height = img.height * (600 / img.width);
                const ctx = canvas.getContext('2d');
                ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
                callback(canvas.toDataURL('image/jpeg', 0.7));
            };
        };
    }

    document.getElementById('fotoKunjungan').addEventListener('change', function() {
        if (this.files[0]) compressImage(this.files[0], (c) => { 
            base64Foto = c; 
            document.getElementById('preview-foto').src = c; 
            document.getElementById('preview-foto').style.display = 'block'; 
        });
    });

    document.getElementById('collectionForm').addEventListener('submit', async function(e) {
        e.preventDefault();
        const btn = document.getElementById('btnSimpan');
        const entri = {
            petugas: petugas, waktu: new Date().toLocaleString('id-ID'),
            nama: document.getElementById('namaNasabah').value,
            bertemu: document.getElementById('bertemuDengan').value,
            status: document.getElementById('statusKunjungan').value,
            hasil: document.getElementById('hasilKunjungan').value,
            solusi: document.getElementById('detailSolusi').value,
            gps: document.getElementById('lokasiGps').value || "0,0", foto: base64Foto
        };
        dataLaporan.unshift(entri);
        localStorage.setItem('laporan_bkk', JSON.stringify(dataLaporan));
        tampilkanData();
        btn.disabled = true; btn.innerText = "SINKRON...";
        try {
            await fetch(SCRIPT_URL, { method: 'POST', mode: 'no-cors', body: JSON.stringify(entri) });
            document.getElementById('syncStatus').innerHTML = "<span class='text-success'>✓ Sinkron Berhasil</span>";
        } catch (err) { document.getElementById('syncStatus').innerHTML = "<span class='text-danger'>! Gagal Cloud</span>"; }
        this.reset(); base64Foto = ""; document.getElementById('preview-foto').style.display = 'none';
        btn.disabled = false; btn.innerText = "SIMPAN & SINKRON";
    });

    function tampilkanData() {
        const table = document.getElementById('tbodyLaporan');
        const search = document.getElementById('cariLaporan').value.toLowerCase();
        table.innerHTML = ""; let cBayar = 0;
        dataLaporan.forEach((item, index) => {
            if (item.nama.toLowerCase().includes(search)) {
                const isBayar = item.status.toLowerCase().match(/bayar|titip/);
                if(isBayar) cBayar++;
                const row = table.insertRow();
                row.insertCell(0).innerHTML = `<img src="${item.foto}" class="img-table" onclick="window.open(this.src)">`;
                row.insertCell(1).innerHTML = `<b>${item.nama}</b><br><small>${item.waktu}</small>`;
                row.insertCell(2).innerText = item.petugas;
                row.insertCell(3).innerHTML = `<span class="badge ${isBayar?'bg-success':'bg-warning text-dark'}">${item.status}</span>`;
                row.insertCell(4).innerHTML = `<button onclick="hapusData(${index})" class="btn btn-xs btn-danger"><i class="bi bi-trash"></i></button>`;
            }
        });
        document.getElementById('countTotal').innerText = dataLaporan.length;
        document.getElementById('countBayar').innerText = cBayar;
        document.getElementById('countPending').innerText = dataLaporan.length - cBayar;
    }

    function hapusData(i) { if(confirm("Hapus?")) { dataLaporan.splice(i,1); localStorage.setItem('laporan_bkk', JSON.stringify(dataLaporan)); tampilkanData(); } }
    function exportToExcel() {
        const ws = XLSX.utils.json_to_sheet(dataLaporan.map(i => ({...i, foto: 'Di Sistem'})));
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "Laporan");
        XLSX.writeFile(wb, `Laporan_BKK.xlsx`);
    }
</script>
</body>
</html>
