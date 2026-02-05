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
        .sticky-table { max-height: 350px; overflow-y: auto; border: 1px solid #dee2e6; border-radius: 8px; }
        .table-fixed { table-layout: fixed; width: 100%; margin-bottom: 0; }
        .table-fixed th, .table-fixed td { overflow: hidden; text-overflow: ellipsis; white-space: nowrap; vertical-align: middle; }
        .btn-nama { text-align: left; font-weight: bold; color: var(--bkk-blue); border: none; background: none; padding: 0; width: 100%; font-size: 0.85rem; }
        .amount-font { font-family: 'Courier New', monospace; font-weight: bold; font-size: 0.85rem; }
        .alamat-truncate { font-size: 0.75rem; color: #666; overflow: hidden; text-overflow: ellipsis; }
        .img-table { width: 45px; height: 45px; object-fit: cover; border-radius: 6px; cursor: pointer; }
        #preview-foto { max-width: 100%; height: 150px; object-fit: cover; display: none; border-radius: 8px; margin-top: 10px; border: 2px solid #ddd; }
        .rekap-box { background: white; border-radius: 10px; padding: 15px; margin-bottom: 20px; border-left: 5px solid var(--bkk-gold); }
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
            <h6 class="fw-bold text-success mb-0"><i class="bi bi-database-check"></i> Rencana Kunjungan</h6>
            <button onclick="tarikMasterCloud()" id="btnTarikCloud" class="btn btn-sm btn-success fw-bold"><i class="bi bi-cloud-download"></i> TARIK CLOUD</button>
        </div>
        <div class="table-responsive sticky-table">
            <table class="table table-sm table-striped table-fixed">
                <thead class="table-success small sticky-top">
                    <tr>
                        <th style="width: 25%">Nama</th>
                        <th style="width: 25%">Alamat</th>
                        <th style="width: 15%" class="text-end">OS</th>
                        <th style="width: 15%" class="text-end">Tagihan</th>
                        <th style="width: 15%" class="text-center">Tgl</th>
                        <th style="width: 5%" class="text-center">WA</th>
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
                    <input type="text" id="namaNasabah" class="form-control mb-2" list="listNasabah" placeholder="Nama Nasabah" required onchange="autoFillAlamat()">
                    <datalist id="listNasabah"></datalist>
                    <textarea id="alamatNasabah" class="form-control mb-3 bg-light" rows="2" placeholder="Alamat" readonly></textarea>

                    <select id="bertemuDengan" class="form-select mb-3" required>
                        <option value="" disabled selected>Bertemu Dengan...</option>
                        <option value="" disabled selected>Bertemu Dengan...</option>
                        <option value="Debitur">Debitur</option>
                        <option value="Pasangan">Pasangan</option>
                        <option value="Orang Tua">Orang Tua</option>
                        <option value="Anak">Anak</option>
                        <option value="Saudara">Saudara</option>
                        <option value="Tetangga">Tetangga</option>
                        <option value="Lainnya">Lainnya</option>
                        <option value="Tidak Ada Orang">Tidak Ada Orang</option>
                    </select>

                    <select id="statusKunjungan" class="form-select mb-3" required onchange="cekStatusJanji()">
                        <option value="" disabled selected>Status Penagihan...</option>
                        <option value="Janji Bayar">Janji Bayar</option>
                        <option value="Bayar Sebagian">Bayar Sebagian</option>
                        <option value="Titip Angsuran">Titip Angsuran</option>
                        <option value="Rumah Kosong">Rumah Kosong</option>
                    </select>

                    <div id="areaNominal" class="mb-3 p-3 border border-primary rounded shadow-sm bg-light" style="display: none;">
                        <div id="boxTglJanji" class="mb-2">
                            <label class="small fw-bold text-danger">TANGGAL JANJI BAYAR:</label>
                            <input type="date" id="tglJanjiBayar" class="form-control form-control-sm">
                        </div>
                        <div id="boxNominal">
                            <label class="small fw-bold text-primary">NOMINAL (Rp):</label>
                            <input type="text" id="nominalDisplay" class="form-control fw-bold text-primary" placeholder="Rp 0" onkeyup="formatRupiah(this)">
                            <input type="hidden" id="nominalAsli">
                        </div>
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
                <div class="text-success"><small>Bayar/Titip</small><h4 id="countBayar" class="fw-bold">0</h4></div>
                <div class="text-danger"><small>Pending</small><h4 id="countPending" class="fw-bold">0</h4></div>
            </div>
            <div class="card p-3">
                <input type="text" id="cariLaporan" class="form-control mb-3" placeholder="Cari Riwayat..." onkeyup="tampilkanData()">
                <div class="table-responsive">
                    <table class="table table-sm table-hover border">
                        <thead class="table-dark small"><tr><th>Foto</th><th>Nasabah & Nominal</th><th>Status</th><th>Aksi</th></tr></thead>
                        <tbody id="tbodyLaporan" class="small"></tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
    const MASTER_PASS = "bkk123";
    // GANTI URL DIBAWAH INI SETIAP KALI DEPLOY ULANG GOOGLE SCRIPT
    const SCRIPT_URL = "https://script.google.com/macros/s/AKfycby6ONHaNKyiiPpJAoVr3yjShDJOtg7iURYJ5qQgMqDJm10a9COD79HAxzGODF85Y86i/exec"; 

    let dataLaporan = JSON.parse(localStorage.getItem('laporan_bkk')) || [];
    let petugas = localStorage.getItem('petugas_aktif') || "";
    let base64Foto = "";

    window.onload = () => { if(petugas) loginSukses(); getGPS(); tampilkanData(); tampilkanMasterExcel(); updateDatalist(); };

    function formatRupiah(el) {
        let val = el.value.replace(/\D/g, "");
        document.getElementById('nominalAsli').value = val;
        el.value = val ? "Rp " + Number(val).toLocaleString('id-ID') : "";
    }

    function cekStatusJanji() {
        const s = document.getElementById('statusKunjungan').value;
        const area = document.getElementById('areaNominal');
        const boxTgl = document.getElementById('boxTglJanji');
        if (["Janji Bayar", "Bayar Sebagian", "Titip Angsuran"].includes(s)) {
            area.style.display = "block";
            boxTgl.style.display = (s === "Janji Bayar") ? "block" : "none";
        } else { area.style.display = "none"; }
    }

    async function tarikMasterCloud() {
        const btn = document.getElementById('btnTarikCloud');
        btn.disabled = true; btn.innerHTML = "...";
        try {
            const res = await fetch(SCRIPT_URL);
            const data = await res.json();
            if (Array.isArray(data)) {
                localStorage.setItem('rencana_kunjungan', JSON.stringify(data));
                tampilkanMasterExcel(); updateDatalist();
                alert("Master Sinkron!");
            }
        } catch (e) { alert("Gagal Tarik Cloud!"); }
        finally { btn.disabled = false; btn.innerHTML = "TARIK CLOUD"; }
    }

    document.getElementById('collectionForm').addEventListener('submit', async function(e) {
        e.preventDefault();
        const btn = document.getElementById('btnSimpan');
        const entri = {
            waktu: new Date().toLocaleString('id-ID'),
            petugas: petugas,
            nama: document.getElementById('namaNasabah').value,
            alamat: document.getElementById('alamatNasabah').value,
            bertemu: document.getElementById('bertemuDengan').value,
            status: document.getElementById('statusKunjungan').value,
            janji_bayar: document.getElementById('tglJanjiBayar').value || "-",
            nominal: document.getElementById('nominalAsli').value || "0",
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
            await fetch(SCRIPT_URL, {
                method: 'POST',
                mode: 'no-cors',
                headers: {'Content-Type': 'text/plain'},
                body: JSON.stringify(entri)
            });
            document.getElementById('syncStatus').innerHTML = "<span class='text-success fw-bold'>✓ Terkirim</span>";
            this.reset();
            document.getElementById('areaNominal').style.display = 'none';
            document.getElementById('preview-foto').style.display = 'none';
            base64Foto = "";
        } catch (err) { alert("Gagal Sinkron!"); }
        btn.disabled = false; btn.innerText = "SIMPAN & SINKRON";
    });

    function tampilkanData() {
        const table = document.getElementById('tbodyLaporan');
        table.innerHTML = ""; let cb = 0;
        dataLaporan.forEach((item, i) => {
            const isB = item.status.match(/Bayar|Titip/); if(isB) cb++;
            const nomText = item.nominal > 0 ? `<br><b class="text-primary">Rp ${Number(item.nominal).toLocaleString('id-ID')}</b>` : "";
            const row = table.insertRow();
            row.innerHTML = `<td><img src="${item.foto}" class="img-table" onclick="window.open(this.src)"></td>
                             <td><b>${item.nama}</b>${nomText}<br><small class="text-muted" style="font-size:0.6rem">${item.waktu}</small></td>
                             <td><span class="badge ${isB?'bg-success':'bg-warning text-dark'}">${item.status}</span></td>
                             <td><button onclick="hapusData(${i})" class="btn btn-sm btn-outline-danger"><i class="bi bi-trash"></i></button></td>`;
        });
        document.getElementById('countTotal').innerText = dataLaporan.length;
        document.getElementById('countBayar').innerText = cb;
        document.getElementById('countPending').innerText = dataLaporan.length - cb;
    }

    function tampilkanMasterExcel() {
        const tbody = document.getElementById('tbodyMasterExcel');
        const data = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
        tbody.innerHTML = "";
        data.forEach(item => {
            const row = tbody.insertRow();
            const wa = item.telp ? item.telp.toString().replace(/^0/, '62') : "";
            row.insertCell(0).innerHTML = `<button type="button" class="btn-nama" onclick="pilihNasabah('${item.nama}')">${item.nama}</button>`;
            row.insertCell(1).innerHTML = `<div class="alamat-truncate">${item.alamat || "-"}</div>`;
            row.insertCell(2).className="text-end amount-font"; row.cells[2].innerText = Number(item.os||0).toLocaleString('id-ID');
            row.insertCell(3).className="text-end amount-font text-danger"; row.cells[3].innerText = Number(item.tagihan||0).toLocaleString('id-ID');
            row.insertCell(4).className="text-center small"; row.cells[4].innerText = item.tanggal || "-";
            row.insertCell(5).className="text-center"; row.cells[5].innerHTML = `<a href="https://wa.me/${wa}" class="text-success"><i class="bi bi-whatsapp"></i></a>`;
        });
    }

    function autoFillAlamat() {
        const nama = document.getElementById('namaNasabah').value;
        const master = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
        const ketemu = master.find(u => u.nama.toLowerCase() === nama.toLowerCase());
        document.getElementById('alamatNasabah').value = ketemu ? ketemu.alamat : "";
    }
    function pilihNasabah(nama) { document.getElementById('namaNasabah').value = nama; autoFillAlamat(); }
    function prosesLogin() { 
        const n = document.getElementById('userKolektor').value;
        const p = document.getElementById('passKolektor').value;
        if (n && p === MASTER_PASS) { localStorage.setItem('petugas_aktif', n); petugas = n; loginSukses(); } else alert("Gagal!");
    }
    function loginSukses() { document.getElementById('loginOverlay').style.display='none'; document.getElementById('labelPetugas').innerText="Petugas: "+petugas; }
    function logout() { localStorage.removeItem('petugas_aktif'); location.reload(); }
    function getGPS() { if(navigator.geolocation) navigator.geolocation.getCurrentPosition(p => document.getElementById('lokasiGps').value=`${p.coords.latitude},${p.coords.longitude}`); }
    function hapusData(i) { if(confirm("Hapus?")) { dataLaporan.splice(i,1); localStorage.setItem('laporan_bkk', JSON.stringify(dataLaporan)); tampilkanData(); } }
    function updateDatalist() {
        const master = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
        const dl = document.getElementById('listNasabah'); dl.innerHTML = "";
        master.forEach(n => { let o = document.createElement('option'); o.value=n.nama; dl.appendChild(o); });
    }
    document.getElementById('fotoKunjungan').addEventListener('change', function() {
        const reader = new FileReader();
        reader.onload = e => {
            const img = new Image(); img.src = e.target.result;
            img.onload = () => {
                const cvs = document.createElement('canvas'); const ctx = cvs.getContext('2d');
                cvs.width = 600; cvs.height = img.height * (600/img.width);
                ctx.drawImage(img, 0, 0, cvs.width, cvs.height);
                base64Foto = cvs.toDataURL('image/jpeg', 0.6);
                document.getElementById('preview-foto').src = base64Foto;
                document.getElementById('preview-foto').style.display='block';
            };
        };
        reader.readAsDataURL(this.files[0]);
    });
</script>
</body>
</html>
