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
        .img-table { width: 50px; height: 50px; object-fit: cover; border-radius: 6px; cursor: pointer; }
        .rekap-box { background: white; border-radius: 10px; padding: 15px; margin-bottom: 20px; border-left: 5px solid var(--bkk-gold); }
        .sticky-table { max-height: 350px; overflow-y: auto; }
    </style>
</head>
<body>

<div id="loginOverlay">
    <div class="card p-4 text-center shadow-lg" style="max-width: 350px; width: 90%;">
        <img src="https://bankbanjarharjo.id/assets/upload/images/logo.png" width="70" class="mx-auto mb-3">
        <h4 class="fw-bold">Akses Kolektor</h4>
        <input type="text" id="userKolektor" class="form-control mb-2 text-center" placeholder="Nama Lengkap">
        <input type="password" id="passKolektor" class="form-control mb-3 text-center" placeholder="Password">
        <button onclick="prosesLogin()" class="btn btn-primary w-100 fw-bold">LOGIN</button>
    </div>
</div>

<div class="header-bank">
    <img src="https://bankbanjarharjo.id/assets/upload/images/logo.png" class="logo-bank">
    <div>
        <h4 class="fw-bold mb-0">BPR BKK JATENG</h4>
        <span id="labelPetugas" class="badge bg-warning text-dark">Petugas: -</span>
    </div>
    <button onclick="logout()" class="btn btn-sm btn-outline-light position-absolute top-0 end-0 m-2"><i class="bi bi-power"></i></button>
</div>

<div class="container-fluid">
    <div class="card p-3 mb-4 border-success">
        <div class="d-flex justify-content-between align-items-center mb-3">
            <h6 class="fw-bold text-success mb-0"><i class="bi bi-database-check"></i> Data Master Rencana Kunjungan</h6>
            <button onclick="tarikMasterCloud()" id="btnTarikCloud" class="btn btn-sm btn-success fw-bold">
                <i class="bi bi-cloud-download"></i> TARIK DATA PUSAT
            </button>
        </div>
        
        <div class="row g-2 mb-3">
            <div class="col-md-3">
                <label class="small fw-bold">Import Excel:</label>
                <input type="file" id="importExcel" class="form-control form-control-sm border-success" accept=".xlsx, .xls">
            </div>
            <div class="col-md-3">
                <label class="small fw-bold">Cari Nama/Alamat:</label>
                <input type="text" id="searchMaster" class="form-control form-control-sm" placeholder="Ketik..." onkeyup="tampilkanMasterExcel()">
            </div>
            <div class="col-md-3">
                <label class="small fw-bold">Filter Tanggal:</label>
                <input type="date" id="filterTglMaster" class="form-control form-control-sm" onchange="tampilkanMasterExcel()">
            </div>
            <div class="col-md-3 text-end">
                <label class="d-none d-md-block">&nbsp;</label>
                <button onclick="hapusSemuaRencana()" class="btn btn-sm btn-danger w-100 fw-bold">HAPUS MASTER</button>
            </div>
        </div>

        <div class="table-responsive sticky-table border rounded">
            <table class="table table-sm table-striped mb-0">
                <thead class="table-success small sticky-top">
                    <tr>
                        <th>Nama (Klik)</th>
                        <th>Alamat</th>
                        <th>OS</th>
                        <th>Telp</th>
                        <th>Tagihan</th>
                        <th>Tgl</th>
                        <th>WA</th>
                    </tr>
                </thead>
                <tbody id="tbodyMasterExcel" class="small"></tbody>
            </table>
        </div>
    </div>

    <div class="row">
        <div class="col-lg-4">
            <div class="card p-4 mb-4">
                <h5 class="fw-bold text-primary mb-3">Laporan Lapangan</h5>
                <form id="collectionForm">
                    <label class="small fw-bold">Nama Nasabah:</label>
                    <input type="text" id="namaNasabah" class="form-control mb-2" list="listNasabah" required onchange="autoFillAlamat()">
                    <datalist id="listNasabah"></datalist>

                    <label class="small fw-bold">Alamat:</label>
                    <textarea id="alamatNasabah" class="form-control mb-3 bg-light" rows="2" readonly></textarea>

                    <select id="bertemuDengan" class="form-select mb-3" required>
                        <option value="" disabled selected>Bertemu Dengan...</option>
                        <option value="Debitur">Debitur</option>
                        <option value="Keluarga">Keluarga</option>
                        <option value="Tidak Ada Orang">Tidak Ada Orang</option>
                    </select>

                    <select id="statusKunjungan" class="form-select mb-3" required onchange="cekStatusJanji()">
                        <option value="" disabled selected>Status Penagihan...</option>
                        <option value="Janji Bayar">Janji Bayar</option>
                        <option value="Bayar Sebagian">Bayar Sebagian</option>
                        <option value="Titip Angsuran">Titip Angsuran</option>
                        <option value="Rumah Kosong">Rumah Kosong</option>
                    </select>

                    <div id="areaJanjiBayar" class="mb-3 p-2 border border-danger rounded shadow-sm" style="display: none;">
                        <label class="small fw-bold text-danger">TANGGAL JANJI BAYAR:</label>
                        <input type="date" id="tglJanjiBayar" class="form-control form-control-sm">
                    </div>

                    <textarea id="hasilKunjungan" class="form-control mb-2" rows="2" placeholder="Hasil Kunjungan..." required></textarea>
                    <textarea id="detailSolusi" class="form-control mb-3" rows="2" placeholder="Rencana RTL..." required></textarea>
                    
                    <input type="file" id="fotoKunjungan" class="form-control form-control-sm mb-2" accept="image/*" capture="camera" required>
                    <img id="preview-foto" class="mx-auto d-block mb-3">
                    
                    <input type="hidden" id="lokasiGps">
                    <button type="submit" id="btnSimpan" class="btn btn-primary btn-lg w-100 fw-bold">SIMPAN & SINKRON</button>
                    <div id="syncStatus" class="text-center mt-2 small"></div>
                </form>
            </div>
        </div>

        <div class="col-lg-8">
            <div class="rekap-box d-flex justify-content-around text-center shadow-sm">
                <div><small>Total</small><h4 id="countTotal" class="fw-bold">0</h4></div>
                <div class="text-success"><small>Bayar</small><h4 id="countBayar" class="fw-bold">0</h4></div>
                <div class="text-danger"><small>Pending</small><h4 id="countPending" class="fw-bold">0</h4></div>
            </div>

            <div class="card p-3">
                <div class="d-flex justify-content-between mb-3">
                    <input type="text" id="cariLaporan" class="form-control w-50" placeholder="Cari Riwayat..." onkeyup="tampilkanData()">
                    <button onclick="exportToExcel()" class="btn btn-success btn-sm fw-bold">UNDUH EXCEL</button>
                </div>
                <div class="table-responsive">
                    <table class="table table-sm table-hover border">
                        <thead class="table-dark small">
                            <tr><th>Foto</th><th>Nasabah</th><th>Status</th><th>Aksi</th></tr>
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
    // GANTI URL DI BAWAH DENGAN URL WEB APP ANDA
    const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbxB0FVMQ6ORqq9JHI2Cu_KOhNPk9nxkmRYRYiMmNYa1JpNznmXT_iBVzOSnHE5miVo0/exec";
    
    let dataLaporan = JSON.parse(localStorage.getItem('laporan_bkk')) || [];
    let petugas = localStorage.getItem('petugas_aktif') || "";
    let base64Foto = "";

    window.onload = () => {
        if(petugas) loginSukses();
        getGPS();
        tampilkanData();
        tampilkanMasterExcel();
        updateDatalist();
    };

    // --- SINKRONISASI CLOUD ---
    async function tarikMasterCloud() {
        const btn = document.getElementById('btnTarikCloud');
        const originalText = btn.innerHTML;
        
        btn.disabled = true;
        btn.innerHTML = '<span class="spinner-border spinner-border-sm"></span> Loading...';

        try {
            const response = await fetch(SCRIPT_URL);
            const dataCloud = await response.json();

            if (Array.isArray(dataCloud) && dataCloud.length > 0) {
                localStorage.setItem('rencana_kunjungan', JSON.stringify(dataCloud));
                tampilkanMasterExcel();
                updateDatalist();
                alert("Berhasil menarik " + dataCloud.length + " data dari Cloud!");
            } else {
                alert("Data Cloud kosong atau Sheet 'Master' tidak ditemukan.");
            }
        } catch (err) {
            console.error(err);
            alert("Gagal koneksi ke Cloud. Periksa internet atau URL Script.");
        } finally {
            btn.disabled = false;
            btn.innerHTML = originalText;
        }
    }

    // --- AUTH ---
    function prosesLogin() {
        const n = document.getElementById('userKolektor').value;
        const p = document.getElementById('passKolektor').value;
        if (n && p === MASTER_PASS) {
            localStorage.setItem('petugas_aktif', n);
            petugas = n;
            loginSukses();
        } else { alert("Login Gagal!"); }
    }
    function loginSukses() {
        document.getElementById('loginOverlay').style.display = 'none';
        document.getElementById('labelPetugas').innerText = "Petugas: " + petugas;
    }
    function logout() { localStorage.removeItem('petugas_aktif'); location.reload(); }
    function getGPS() {
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(pos => {
                document.getElementById('lokasiGps').value = `${pos.coords.latitude},${pos.coords.longitude}`;
            });
        }
    }

    // --- EXCEL & MASTER LOGIC ---
    document.getElementById('importExcel').addEventListener('change', function(e) {
        const file = e.target.files[0];
        const reader = new FileReader();
        reader.onload = (event) => {
            const data = new Uint8Array(event.target.result);
            const workbook = XLSX.read(data, { type: 'array', cellDates: true });
            const rows = XLSX.utils.sheet_to_json(workbook.Sheets[workbook.SheetNames[0]], { header: 1 });
            const rencana = [];
            for (let i = 1; i < rows.length; i++) {
                if(rows[i][0]) {
                    rencana.push({
                        nama: String(rows[i][0]),
                        alamat: rows[i][1] || "-",
                        os: rows[i][2] || 0,
                        telp: rows[i][3] || "-",
                        tagihan: rows[i][4] || 0,
                        tanggal: formatTgl(rows[i][5])
                    });
                }
            }
            localStorage.setItem('rencana_kunjungan', JSON.stringify(rencana));
            tampilkanMasterExcel();
            updateDatalist();
            alert("Master Lokal Diupdate!");
        };
        reader.readAsArrayBuffer(file);
    });

    function formatTgl(v) {
        if(v instanceof Date) return v.toISOString().split('T')[0];
        return String(v);
    }

    function tampilkanMasterExcel() {
    const tbody = document.getElementById('tbodyMasterExcel');
    const search = document.getElementById('searchMaster').value.toLowerCase();
    const filterTgl = document.getElementById('filterTglMaster').value;
    const data = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
    
    tbody.innerHTML = "";
    
    // Filter Data
    const filtered = data.filter(i => {
        const matchSearch = i.nama.toLowerCase().includes(search) || i.alamat.toLowerCase().includes(search);
        const matchDate = filterTgl ? i.tanggal === filterTgl : true;
        return matchSearch && matchDate;
    });

    if(filtered.length === 0) {
        tbody.innerHTML = "<tr><td colspan='6' class='text-center py-4 text-muted small'>Tidak ada data rencana kunjungan</td></tr>";
        return;
    }

    // Render Baris Tabel
    filtered.forEach(item => {
        const row = tbody.insertRow();
        const waLink = item.telp.toString().replace(/^0/, '62').replace(/\D/g, '');
        
        // Format mata uang IDR
        const osFormatted = Number(item.os).toLocaleString('id-ID');
        const tagihanFormatted = Number(item.tagihan).toLocaleString('id-ID');

        row.innerHTML = `
            <td>
                <button type="button" class="btn-nama" onclick="pilihNasabah('${item.nama.replace(/'/g, "\\'")}')">
                    ${item.nama}
                </button>
            </td>
            <td><div class="alamat-truncate">${item.alamat}</div></td>
            <td class="text-end amount-font">${osFormatted}</td>
            <td class="text-end amount-font"><span class="tagihan-highlight">${tagihanFormatted}</span></td>
            <td class="text-center">
                <span class="badge rounded-pill bg-light text-dark border" style="font-size: 0.7rem;">${item.tanggal}</span>
            </td>
            <td class="text-center">
                <a href="https://wa.me/${waLink}" target="_blank" class="btn btn-sm btn-outline-success border-0">
                    <i class="bi bi-whatsapp"></i>
                </a>
            </td>
        `;
    });
}

    function updateDatalist() {
        const master = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
        const dl = document.getElementById('listNasabah');
        dl.innerHTML = "";
        master.forEach(n => {
            let opt = document.createElement('option');
            opt.value = n.nama;
            dl.appendChild(opt);
        });
    }

    function autoFillAlamat() {
        const nama = document.getElementById('namaNasabah').value;
        const master = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
        const ketemu = master.find(u => u.nama.toLowerCase() === nama.toLowerCase());
        document.getElementById('alamatNasabah').value = ketemu ? (ketemu.alamat || ketemu.ALAMAT) : "";
    }

    function pilihNasabah(nama) {
        document.getElementById('namaNasabah').value = nama;
        autoFillAlamat();
        window.scrollTo({ top: document.getElementById('collectionForm').offsetTop - 20, behavior: 'smooth' });
    }

    function cekStatusJanji() {
        document.getElementById('areaJanjiBayar').style.display = (document.getElementById('statusKunjungan').value === "Janji Bayar") ? "block" : "none";
    }

    // --- FOTO LOGIC ---
    document.getElementById('fotoKunjungan').addEventListener('change', function() {
        const reader = new FileReader();
        reader.onload = e => {
            const img = new Image(); img.src = e.target.result;
            img.onload = () => {
                const canvas = document.createElement('canvas');
                canvas.width = 600; canvas.height = img.height * (600 / img.width);
                canvas.getContext('2d').drawImage(img, 0, 0, canvas.width, canvas.height);
                base64Foto = canvas.toDataURL('image/jpeg', 0.6);
                document.getElementById('preview-foto').src = base64Foto;
                document.getElementById('preview-foto').style.display = 'block';
            };
        };
        reader.readAsDataURL(this.files[0]);
    });

    // --- FORM SUBMIT ---
    document.getElementById('collectionForm').addEventListener('submit', async function(e) {
        e.preventDefault();
        const btn = document.getElementById('btnSimpan');
        const entri = {
            petugas: petugas,
            waktu: new Date().toLocaleString('id-ID'),
            nama: document.getElementById('namaNasabah').value,
            alamat: document.getElementById('alamatNasabah').value,
            bertemu: document.getElementById('bertemuDengan').value,
            status: document.getElementById('statusKunjungan').value,
            janji_bayar: document.getElementById('tglJanjiBayar').value || "-",
            hasil: document.getElementById('hasilKunjungan').value,
            solusi: document.getElementById('detailSolusi').value,
            gps: document.getElementById('lokasiGps').value || "0,0",
            foto: base64Foto
        };

        dataLaporan.unshift(entri);
        localStorage.setItem('laporan_bkk', JSON.stringify(dataLaporan));
        tampilkanData();

        btn.disabled = true; btn.innerText = "SINKRON...";
        try {
            await fetch(SCRIPT_URL, { method: 'POST', mode: 'no-cors', body: JSON.stringify(entri) });
            document.getElementById('syncStatus').innerHTML = "<span class='text-success fw-bold'>✓ Sinkron Berhasil</span>";
            this.reset();
            base64Foto = "";
            document.getElementById('preview-foto').style.display = 'none';
            document.getElementById('areaJanjiBayar').style.display = 'none';
        } catch (err) {
            document.getElementById('syncStatus').innerHTML = "<span class='text-danger'>! Gagal Cloud (Tersimpan Lokal)</span>";
        }
        btn.disabled = false; btn.innerText = "SIMPAN & SINKRON";
    });

    function tampilkanData() {
        const table = document.getElementById('tbodyLaporan');
        const search = document.getElementById('cariLaporan').value.toLowerCase();
        table.innerHTML = ""; let cb = 0;
        dataLaporan.forEach((item, i) => {
            if (item.nama.toLowerCase().includes(search)) {
                const isB = item.status.match(/Bayar|Titip/);
                if(isB) cb++;
                const row = table.insertRow();
                row.innerHTML = `
                    <td><img src="${item.foto}" class="img-table" onclick="window.open(this.src)"></td>
                    <td><b>${item.nama}</b><br><small class="text-muted">${item.waktu}</small></td>
                    <td><span class="badge ${isB?'bg-success':'bg-warning text-dark'}">${item.status}</span></td>
                    <td><button onclick="hapusData(${i})" class="btn btn-sm btn-outline-danger"><i class="bi bi-trash"></i></button></td>
                `;
            }
        });
        document.getElementById('countTotal').innerText = dataLaporan.length;
        document.getElementById('countBayar').innerText = cb;
        document.getElementById('countPending').innerText = dataLaporan.length - cb;
    }

    function hapusSemuaRencana() { if(confirm("Hapus Semua Master?")) { localStorage.removeItem('rencana_kunjungan'); tampilkanMasterExcel(); updateDatalist(); } }
    function hapusData(i) { if(confirm("Hapus data riwayat ini?")) { dataLaporan.splice(i,1); localStorage.setItem('laporan_bkk', JSON.stringify(dataLaporan)); tampilkanData(); } }
    function exportToExcel() {
        const ws = XLSX.utils.json_to_sheet(dataLaporan.map(i => ({...i, foto: 'Lihat di Cloud'})));
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "Laporan");
        XLSX.writeFile(wb, `Laporan_Kolektor_${new Date().toLocaleDateString()}.xlsx`);
    }
</script>
</body>
</html>
