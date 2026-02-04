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
        .btn-sync-manual { position: absolute; bottom: -10px; right: 10px; font-size: 10px; }
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
    <div class="card p-3 mb-4 border-success position-relative">
        <div class="d-flex justify-content-between align-items-center mb-3">
            <h6 class="fw-bold text-success mb-0"><i class="bi bi-cloud-check"></i> Master Rencana Kunjungan (Cloud)</h6>
            <button onclick="tarikMasterOtomatis()" class="btn btn-xs btn-outline-success py-0" style="font-size: 12px;"><i class="bi bi-arrow-clockwise"></i> Refresh Cloud</button>
        </div>
        
        <div class="row g-2 mb-3">
            <div class="col-md-6">
                <input type="text" id="searchMaster" class="form-control form-control-sm" placeholder="Cari Nama/Alamat..." onkeyup="tampilkanMasterExcel()">
            </div>
            <div class="col-md-3">
                <input type="date" id="filterTglMaster" class="form-control form-control-sm" onchange="tampilkanMasterExcel()">
            </div>
            <div class="col-md-3">
                <button onclick="hapusSemuaRencana()" class="btn btn-sm btn-danger w-100 fw-bold">HAPUS LOKAL</button>
            </div>
        </div>
        
        <div class="table-responsive sticky-table border rounded">
            <table class="table table-sm table-striped mb-0">
                <thead class="table-success small sticky-top">
                    <tr>
                        <th>Nasabah</th>
                        <th>Alamat</th>
                        <th>OS</th>
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

                    <div class="row g-2 mb-3">
                        <div class="col-6">
                            <select id="bertemuDengan" class="form-select form-select-sm" required>
                                <option value="" disabled selected>Bertemu...</option>
                                <option value="Debitur">Debitur</option>
                                <option value="Keluarga">Keluarga</option>
                                <option value="Tidak Ada Orang">Tidak Ada</option>
                            </select>
                        </div>
                        <div class="col-6">
                            <select id="statusKunjungan" class="form-select form-select-sm" required onchange="cekStatusJanji()">
                                <option value="" disabled selected>Status...</option>
                                <option value="Janji Bayar">Janji Bayar</option>
                                <option value="Bayar Sebagian">Bayar Sebagian</option>
                                <option value="Titip Angsuran">Titip Angsuran</option>
                                <option value="Rumah Kosong">Rumah Kosong</option>
                            </select>
                        </div>
                    </div>

                    <div id="areaJanjiBayar" class="mb-3 p-2 border border-danger rounded shadow-sm" style="display: none;">
                        <label class="small fw-bold text-danger">TGL JANJI BAYAR:</label>
                        <input type="date" id="tglJanjiBayar" class="form-control form-control-sm">
                    </div>

                    <textarea id="hasilKunjungan" class="form-control mb-2" rows="2" placeholder="Hasil Kunjungan..." required></textarea>
                    <textarea id="detailSolusi" class="form-control mb-3" rows="2" placeholder="Rencana Tindak Lanjut..." required></textarea>
                    
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
    // --- KONFIGURASI ---
    const MASTER_PASS = "bkk123";
    // Gunakan URL /exec hasil deploy Apps Script Anda
    const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbzBTVPy0UP9C08U1sBZFPR2_yGJgmRqVPVRSn3IUBaEe9wOezs-iMLu2n5_gSNSSQnlfQ/exec"; 

    let dataLaporan = JSON.parse(localStorage.getItem('laporan_bkk')) || [];
    let petugas = localStorage.getItem('petugas_aktif') || "";
    let base64Foto = "";

    window.onload = () => {
        if(petugas) {
            loginSukses();
            tarikMasterOtomatis();
        }
        getGPS();
        tampilkanData();
    };

    // --- LOGIKA SINKRONISASI ---
    

    async function tarikMasterOtomatis() {
        const label = document.getElementById('labelPetugas');
        try {
            label.innerHTML = `<span class="spinner-border spinner-border-sm"></span> Sync...`;
            const response = await fetch(SCRIPT_URL);
            const dataTerbaru = await response.json();
            
            if (Array.isArray(dataTerbaru)) {
                localStorage.setItem('rencana_kunjungan', JSON.stringify(dataTerbaru));
                tampilkanMasterExcel();
                updateDatalist();
            }
        } catch (err) {
            console.warn("Offline: Menggunakan cache lokal.");
            tampilkanMasterExcel();
            updateDatalist();
        } finally {
            label.innerText = "Petugas: " + petugas;
        }
    }

    // --- FORM SUBMIT (PERBAIKAN SINKRONISASI) ---
    document.getElementById('collectionForm').addEventListener('submit', async function(e) {
        e.preventDefault();
        const btn = document.getElementById('btnSimpan');
        const statusDiv = document.getElementById('syncStatus');
        
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

        // 1. Simpan ke Lokal (Safety First jika internet mati)
        dataLaporan.unshift(entri);
        localStorage.setItem('laporan_bkk', JSON.stringify(dataLaporan));
        tampilkanData();

        // 2. Kirim ke Cloud
        btn.disabled = true;
        btn.innerText = "MENGIRIM...";
        statusDiv.innerHTML = "⏳ Menghubungkan ke server...";

        try {
            await fetch(SCRIPT_URL, {
                method: 'POST',
                mode: 'no-cors', 
                body: JSON.stringify(entri),
                headers: { 'Content-Type': 'text/plain' }
            });

            statusDiv.innerHTML = "<span class='text-success fw-bold'>✅ Terkirim ke Cloud!</span>";
            this.reset();
            base64Foto = "";
            document.getElementById('preview-foto').style.display = 'none';
        } catch (err) {
            statusDiv.innerHTML = "<span class='text-danger fw-bold'>⚠️ Gagal Sinkron, data tersimpan di HP.</span>";
        } finally {
            btn.disabled = false;
            btn.innerText = "SIMPAN & SINKRON";
        }
    });

    // --- FUNGSI UI LAINNYA ---
    function prosesLogin() {
        const n = document.getElementById('userKolektor').value;
        const p = document.getElementById('passKolektor').value;
        if (n && p === MASTER_PASS) {
            localStorage.setItem('petugas_aktif', n);
            petugas = n;
            loginSukses();
            tarikMasterOtomatis();
        } else { alert("Login Gagal!"); }
    }

    function loginSukses() {
        document.getElementById('loginOverlay').style.display = 'none';
        document.getElementById('labelPetugas').innerText = "Petugas: " + petugas;
    }

    function logout() { if(confirm("Keluar?")) { localStorage.removeItem('petugas_aktif'); location.reload(); } }

    function tampilkanMasterExcel() {
        const tbody = document.getElementById('tbodyMasterExcel');
        const search = document.getElementById('searchMaster').value.toLowerCase();
        const data = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
        tbody.innerHTML = "";
        data.filter(i => i.nama.toLowerCase().includes(search) || i.alamat.toLowerCase().includes(search))
            .forEach(item => {
                const row = tbody.insertRow();
                const wa = item.telp ? item.telp.toString().replace(/^0/, '62') : "";
                row.innerHTML = `
                    <td><button type="button" class="btn btn-sm btn-link p-0 fw-bold text-decoration-none" onclick="pilihNasabah('${item.nama.replace(/'/g, "\\'")}')">${item.nama}</button></td>
                    <td class="text-truncate" style="max-width:120px; font-size:10px;">${item.alamat}</td>
                    <td class="text-end small">${Number(item.os || 0).toLocaleString('id-ID')}</td>
                    <td class="text-end text-danger fw-bold small">${Number(item.tagihan || 0).toLocaleString('id-ID')}</td>
                    <td><small>${item.tanggal || '-'}</small></td>
                    <td class="text-center"><a href="https://wa.me/${wa}" target="_blank" class="text-success"><i class="bi bi-whatsapp"></i></a></td>`;
            });
    }

    function updateDatalist() {
        const master = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
        document.getElementById('listNasabah').innerHTML = master.map(n => `<option value="${n.nama}">`).join('');
    }

    function pilihNasabah(nama) {
        document.getElementById('namaNasabah').value = nama;
        autoFillAlamat();
        window.scrollTo({ top: document.getElementById('collectionForm').offsetTop - 20, behavior: 'smooth' });
    }

    function autoFillAlamat() {
        const nama = document.getElementById('namaNasabah').value;
        const master = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
        const ketemu = master.find(u => u.nama.toLowerCase() === nama.toLowerCase());
        document.getElementById('alamatNasabah').value = ketemu ? ketemu.alamat : "";
    }

    function cekStatusJanji() {
        document.getElementById('areaJanjiBayar').style.display = (document.getElementById('statusKunjungan').value === "Janji Bayar") ? "block" : "none";
    }

    function getGPS() {
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(pos => {
                document.getElementById('lokasiGps').value = `${pos.coords.latitude},${pos.coords.longitude}`;
            });
        }
    }

    document.getElementById('fotoKunjungan').addEventListener('change', function() {
        const reader = new FileReader();
        reader.onload = e => {
            const img = new Image(); img.src = e.target.result;
            img.onload = () => {
                const canvas = document.createElement('canvas');
                const MAX_W = 600;
                canvas.width = MAX_W;
                canvas.height = img.height * (MAX_W / img.width);
                canvas.getContext('2d').drawImage(img, 0, 0, canvas.width, canvas.height);
                base64Foto = canvas.toDataURL('image/jpeg', 0.7);
                document.getElementById('preview-foto').src = base64Foto;
                document.getElementById('preview-foto').style.display = 'block';
            };
        };
        if(this.files[0]) reader.readAsDataURL(this.files[0]);
    });

    function tampilkanData() {
        const table = document.getElementById('tbodyLaporan');
        table.innerHTML = ""; 
        let cb = 0;
        dataLaporan.forEach((item, i) => {
            const isB = item.status.match(/Bayar|Titip/);
            if(isB) cb++;
            const row = table.insertRow();
            row.innerHTML = `
                <td><img src="${item.foto}" class="img-table" onclick="window.open(this.src)"></td>
                <td><b>${item.nama}</b><br><small class="text-muted">${item.waktu}</small></td>
                <td><span class="badge ${isB?'bg-success':'bg-warning text-dark'}">${item.status}</span></td>
                <td><button onclick="hapusData(${i})" class="btn btn-sm btn-outline-danger"><i class="bi bi-trash"></i></button></td>`;
        });
        document.getElementById('countTotal').innerText = dataLaporan.length;
        document.getElementById('countBayar').innerText = cb;
        document.getElementById('countPending').innerText = dataLaporan.length - cb;
    }

    function hapusData(i) { if(confirm("Hapus?")) { dataLaporan.splice(i,1); localStorage.setItem('laporan_bkk', JSON.stringify(dataLaporan)); tampilkanData(); } }
    function hapusSemuaRencana() { if(confirm("Hapus cache lokal?")) { localStorage.removeItem('rencana_kunjungan'); location.reload(); } }

    function exportToExcel() {
        const ws = XLSX.utils.json_to_sheet(dataLaporan.map(i => { const {foto, ...rest} = i; return rest; }));
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "Laporan");
        XLSX.writeFile(wb, `Laporan_${petugas}_${new Date().toLocaleDateString()}.xlsx`);
    }
</script>
</body>
</html>
