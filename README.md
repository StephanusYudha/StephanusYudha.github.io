<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Amore - Enterprise System</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.1/font/bootstrap-icons.css">
    <style>
        :root { --bkk-blue: #003366; --bkk-gold: #FFD700; --bkk-light: #f1f5f9; --bkk-success: #198754; --bkk-danger: #dc3545; }
        body { background: var(--bkk-light); font-family: 'Inter', sans-serif; padding-bottom: 90px; overflow-x: hidden; }
        
        .header-app { 
            background: linear-gradient(135deg, var(--bkk-blue) 0%, #004a99 100%); 
            color: white; padding: 30px 20px 70px; 
            border-bottom: 5px solid var(--bkk-gold); 
            border-radius: 0 0 35px 35px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
        }
        .stats-card { 
            background: white; border-radius: 20px; padding: 22px; 
            margin: -45px 15px 25px; 
            box-shadow: 0 10px 30px rgba(0,0,0,0.08); 
            border: 1px solid rgba(0,0,0,0.05);
        }

        .section { display: none; animation: slideUp 0.4s ease-out; }
        .section.active { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        .form-control, .form-select { border-radius: 10px; padding: 12px; border: 1px solid #e2e8f0; font-size: 0.95rem; }
        .preview-box { 
            width: 100%; height: 200px; background: #f8fafc; 
            border: 2px dashed #cbd5e1; border-radius: 18px; 
            display: flex; align-items: center; justify-content: center; 
            overflow: hidden; cursor: pointer;
        }
        .preview-box img { width: 100%; height: 100%; object-fit: cover; }

        .bottom-nav { 
            position: fixed; bottom: 0; width: 100%; 
            background: rgba(255,255,255,0.95); backdrop-filter: blur(10px);
            display: flex; padding: 10px 0; border-top: 1px solid #e2e8f0; 
            z-index: 1000;
        }
        .nav-link { flex: 1; text-align: center; color: #64748b; text-decoration: none; font-size: 0.65rem; font-weight: 700; }
        .nav-link.active { color: var(--bkk-blue); }
        .nav-link i { font-size: 1.3rem; display: block; }

        #loader { 
            display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.85); 
            z-index: 11000; flex-direction: column; align-items: center; justify-content: center; 
            color: white; backdrop-filter: blur(8px); 
        }
    </style>
</head>
<body>

<div id="loader">
    <div class="spinner-grow text-warning mb-3"></div>
    <h6 id="loaderText" class="fw-bold">Memproses Data...</h6>
</div>

<div id="pageLogin" style="position:fixed; inset:0; background:var(--bkk-light); z-index:10000; display:flex; align-items:center;">
    <div class="container p-4">
        <div class="text-center mb-5">
            <div class="bg-primary d-inline-block p-4 rounded-5 mb-3 shadow"><i class="bi bi-bank2 text-white fs-1"></i></div>
            <h2 class="fw-bold m-0" style="color: var(--bkk-blue);">AMORE</h2>
            <p class="text-muted small">Enterprise Reporting v2.5</p>
        </div>
        <form id="formLogin" class="bg-white p-4 rounded-4 shadow-sm border">
            <input type="text" id="user" class="form-control mb-3" placeholder="Username" required>
            <input type="password" id="pass" class="form-control mb-4" placeholder="Password" required>
            <button type="submit" class="btn btn-primary w-100 py-3 fw-bold rounded-3">LOG IN SYSTEM</button>
        </form>
    </div>
</div>

<div class="header-app d-flex justify-content-between align-items-start">
    <div><h5 class="fw-bold mb-0">AMORE SYSTEM</h5><small id="userGreet" class="opacity-75">Halo, Petugas</small></div>
    <div class="bg-white rounded-circle p-2 shadow-sm text-center" style="width: 40px; height: 40px; cursor:pointer;" onclick="logout()">
        <i class="bi bi-power text-danger fs-5"></i>
    </div>
</div>

<!-- PAGE HOME -->
<div id="pageHome" class="section active">
    <div class="stats-card">
        <small class="text-muted fw-bold">REALISASI SETORAN HARI INI</small>
        <h2 id="dashSetoran" class="fw-bold text-primary my-1">Rp 0</h2>
        <div class="progress mt-3" style="height: 10px; border-radius: 10px;">
            <div id="barTarget" class="progress-bar progress-bar-striped progress-bar-animated bg-success" style="width: 0%"></div>
        </div>
        <div class="text-end mt-1"><small id="txtPersen" class="fw-bold text-success">0% dari Target</small></div>
    </div>
    
    <div class="container mt-4 px-4">
        <h6 class="fw-bold text-muted mb-3">LAYANAN UTAMA</h6>
        <div class="row g-3">
            <div class="col-6" onclick="showPage('pageColl')" style="cursor:pointer;">
                <div class="card border-0 shadow-sm text-center p-4 rounded-4 border-bottom border-4 border-primary">
                    <i class="bi bi-wallet2 fs-1 text-primary mb-2"></i><span class="small fw-bold">KOLEKSI</span>
                </div>
            </div>
            <div class="col-6" onclick="showPage('pageKredit')" style="cursor:pointer;">
                <div class="card border-0 shadow-sm text-center p-4 rounded-4 border-bottom border-4 border-danger">
                    <i class="bi bi-cash-coin fs-1 text-danger mb-2"></i><span class="small fw-bold">KREDIT</span>
                </div>
            </div>
            <div class="col-12" onclick="showPage('pageMkt')" style="cursor:pointer;">
                <div class="card border-0 shadow-sm text-center p-3 rounded-4 border-bottom border-4 border-success">
                    <div class="d-flex align-items-center justify-content-center">
                        <i class="bi bi-person-plus fs-2 text-success me-3"></i><span class="small fw-bold">PROSPEK FUNDING (TABUNGAN/DEPOSITO)</span>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<!-- PAGE KOLEKSI -->
<div id="pageColl" class="section p-3">
    <div class="card border-0 shadow-sm rounded-4 p-4">
        <div class="d-flex align-items-center mb-4 text-primary" onclick="showPage('pageHome')" style="cursor:pointer;">
            <i class="bi bi-arrow-left-circle-fill fs-4 me-2"></i><h5 class="fw-bold mb-0">Laporan Koleksi</h5>
        </div>
        <form id="formCollection">
            <div class="mb-3">
                <label class="small fw-bold text-muted">NAMA NASABAH</label>
                <input type="text" id="n_coll" class="form-control" list="dataNasabah" placeholder="Cari nasabah..." required>
                <datalist id="dataNasabah"></datalist>
            </div>
            <div class="row g-2 mb-3">
                <div class="col-6">
                    <label class="small fw-bold text-muted">Status</label>
                    <select id="s_coll" class="form-select py-2" required>
                        <option value="Titip Angsuran">Titip Angsuran</option>
                        <option value="Bayar Sebagian">Bayar Sebagian</option>
                        <option value="Janji Bayar">Janji Bayar</option>
                        <option value="Rumah Kosong">Rumah Kosong</option>
                    </select>
                </div>
                <div class="col-6">
                    <label class="small fw-bold text-muted">Bertemu</label>
                    <select id="bertemu" class="form-select py-2">
                        <option value="Debitur">Debitur</option>
                        <option value="Pasangan">Pasangan</option>
                        <option value="Keluarga">Keluarga</option>
                        <option value="Tetangga">Tetangga</option>
                    </select>
                </div>
            </div>

            <div id="areaNominal" class="p-3 border rounded bg-light mb-3">
                <div id="boxJanji" style="display:none;" class="mb-2">
                    <label class="small fw-bold text-danger">Tanggal Janji Bayar:</label>
                    <input type="date" id="tglJanji" class="form-control">
                </div>
                <div id="boxNominal">
                    <label class="small fw-bold text-primary">Nominal Pembayaran (Rp):</label>
                    <input type="text" id="nom_coll_display" class="form-control fw-bold" placeholder="Rp 0" onkeyup="formatRupiah(this)">
                </div>
            </div>

            <div class="mb-3">
                <label class="small fw-bold">Jenis Usaha:</label>
                <select id="usahaDebitur" class="form-select" required>
                    <option value="" disabled selected>Pilih Usaha...</option>
                    <option value="Perdagangan">Perdagangan</option>
                    <option value="Pertanian dan Perkebunan">Pertanian dan Perkebunan</option>
                    <option value="Peternakan">Peternakan</option>
                    <option value="Jasa">Jasa</option>
                    <option value="Industri dan Produksi">Industri dan Produksi</option>
                    <option value="Kuliner dan F&B">Kuliner dan F&B</option>
                </select>
            </div>

            <div class="mb-3">
                <label class="small fw-bold">Penyebab / Kendala:</label>
                <select id="penyebab" class="form-select mb-2" required>
                    <option value="" disabled selected>Penyebab Tidak Bayar...</option>
                    <option value="Omset Menurun/Usaha Sepi">Omset Menurun/Usaha Sepi</option>
                    <option value="Banyak Hutang">Banyak Hutang</option>
                    <option value="Sakit">Sakit</option>
                    <option value="Gagal Panen">Gagal Panen</option>
                </select>
                <select id="kendala" class="form-select mb-3" required>
                    <option value="" disabled selected>Kendala Penyelesaian ...</option>
                    <option value="Debitur Tidak Kooperatif">Debitur Tidak Kooperatif</option>
                    <option value="Janji Palsu">Debitur Hanya Memberikan Janji Palsu</option>
                    <option value="Omset Menurun">Omset Menurun Signifikan</option>
                </select>
            </div>

            <div class="mb-4">
                <div class="preview-box" id="box_coll" onclick="document.getElementById('f_coll').click()">
                    <i class="bi bi-camera-fill fs-1 text-muted"></i>
                </div>
                <input type="file" id="f_coll" class="d-none" accept="image/*" capture="camera" onchange="handleFile(this, 'box_coll')">
            </div>
            <button type="submit" class="btn btn-primary w-100 py-3 fw-bold rounded-3 shadow">KIRIM KOLEKSI</button>
        </form>
    </div>
</div>

<!-- PAGE PROSPEK KREDIT -->
<div id="pageKredit" class="section p-3">
    <div class="card border-0 shadow-sm rounded-4 p-4">
        <div class="d-flex align-items-center mb-4 text-danger" onclick="showPage('pageHome')" style="cursor:pointer;">
            <i class="bi bi-arrow-left-circle-fill fs-4 me-2"></i><h5 class="fw-bold mb-0">Prospek Kredit Baru</h5>
        </div>
        <form id="formKredit">
            <div class="mb-3">
                <label class="small fw-bold">NAMA CALON DEBITUR</label>
                <input type="text" id="n_kredit" class="form-control" placeholder="Nama lengkap sesuai KTP" required>
            </div>
            <div class="mb-3">
                <label class="small fw-bold">NO. WHATSAPP (WA)</label>
                <input type="tel" id="wa_kredit" class="form-control" placeholder="0812..." required>
            </div>
            <div class="mb-3">
                <label class="small fw-bold">ESTIMASI PLAFON PINJAMAN (Rp)</label>
                <input type="text" id="plafon_kredit" class="form-control fw-bold text-danger" placeholder="Rp 0" onkeyup="formatRupiah(this)" required>
            </div>
            <div class="mb-3">
                <label class="small fw-bold">JANGKA WAKTU (TENOR)</label>
                <select id="tenor_kredit" class="form-select" required>
                    <option value="" disabled selected>Pilih Tenor...</option>
                    <option value="6 Bulan">6 Bulan</option>
                    <option value="12 Bulan">12 Bulan</option>
                    <option value="18 Bulan">18 Bulan</option>
                    <option value="24 Bulan">24 Bulan</option>
                    <option value="36 Bulan">36 Bulan</option>
                </select>
            </div>
            <div class="mb-3">
                <label class="small fw-bold">TUJUAN PENGGUNAAN KREDIT</label>
                <select id="tujuan_kredit" class="form-select" required>
                    <option value="" disabled selected>Pilih Tujuan...</option>
                    <option value="Modal Kerja Usaha">Modal Kerja Usaha</option>
                    <option value="Investasi / Pembelian Alat">Investasi / Pembelian Alat</option>
                    <option value="Konsumtif / Renovasi Rumah">Konsumtif / Renovasi Rumah</option>
                    <option value="Lainnya">Lainnya</option>
                </select>
            </div>
            <div class="p-3 border rounded bg-light mb-3">
                <label class="small fw-bold mb-2 text-muted"><i class="bi bi-geo-alt-fill"></i> ALAMAT LOKASI USAHA / RUMAH</label>
                <textarea id="alamat_kredit" class="form-control mb-2" rows="2" placeholder="Jalan / RT / RW" required></textarea>
                <div class="row g-2">
                    <div class="col-6"><input type="text" id="kel_kredit" class="form-control form-control-sm" placeholder="Kelurahan" required></div>
                    <div class="col-6"><input type="text" id="kota_kredit" class="form-control form-control-sm" placeholder="Kota/Kab" required></div>
                </div>
            </div>
            <div class="mb-4">
                <label class="small fw-bold text-muted mb-1">FOTO LOKASI USAHA / RUMAH CALON NASABAH</label>
                <div class="preview-box" id="box_kredit" onclick="document.getElementById('f_kredit').click()">
                    <i class="bi bi-camera-fill fs-1 text-danger"></i>
                </div>
                <input type="file" id="f_kredit" class="d-none" accept="image/*" capture="camera" onchange="handleFile(this, 'box_kredit')">
            </div>
            <button type="submit" class="btn btn-danger w-100 py-3 fw-bold rounded-3 shadow">KIRIM PROSPEK KREDIT</button>
        </form>
    </div>
</div>

<!-- PAGE PROSPEK FUNDING -->
<div id="pageMkt" class="section p-3">
    <div class="card border-0 shadow-sm rounded-4 p-4">
        <div class="d-flex align-items-center mb-4 text-success" onclick="showPage('pageHome')" style="cursor:pointer;">
            <i class="bi bi-arrow-left-circle-fill fs-4 me-2"></i><h5 class="fw-bold mb-0">Prospek Funding</h5>
        </div>
        <form id="formMarketing">
            <div class="mb-3">
                <label class="small fw-bold">NAMA CALON NASABAH</label>
                <input type="text" id="n_mkt" class="form-control" placeholder="Nama lengkap" required>
            </div>
            <div class="mb-3">
                <label class="small fw-bold">NO. WHATSAPP (WA)</label>
                <input type="tel" id="wa_mkt" class="form-control" placeholder="0812..." required>
            </div>
            <div class="p-3 border rounded bg-light mb-3">
                <label class="small fw-bold mb-2 text-muted"><i class="bi bi-geo-alt-fill"></i> ALAMAT LENGKAP</label>
                <textarea id="alamat_mkt" class="form-control mb-2" rows="2" placeholder="Jalan / RT / RW" required></textarea>
                <div class="row g-2">
                    <div class="col-6"><input type="text" id="kel_mkt" class="form-control form-control-sm" placeholder="Kelurahan" required></div>
                    <div class="col-6"><input type="text" id="kota_mkt" class="form-control form-control-sm" placeholder="Kota/Kab" required></div>
                </div>
            </div>
            <div class="mb-3">
                <label class="small fw-bold">MINAT TABUNGAN</label>
                <select id="tab_mkt" class="form-select select-prospek">
                    <option value="Tidak Berminat">--- Tidak Berminat ---</option>
                    <option value="Tabungan Umum">Tabungan Umum</option>
                    <option value="Tabungan Arisan">Tabungan Arisan</option>
                    <option value="Tabungan Pelajar">Tabungan Pelajar</option>
                </select>
            </div>
            <div class="mb-3">
                <label class="small fw-bold">MINAT DEPOSITO</label>
                <select id="dep_mkt" class="form-select select-prospek">
                    <option value="Tidak Berminat">--- Tidak Berminat ---</option>
                    <option value="Deposito 1-3 Bln">Deposito 1-3 Bulan</option>
                    <option value="Deposito 6 Bln">Deposito 6 Bulan</option>
                </select>
            </div>
            <div id="areaNominalMkt" class="p-3 border rounded bg-light mb-3" style="display:none;">
                <label class="small fw-bold text-success">RENCANA NOMINAL (Rp):</label>
                <input type="text" id="nom_mkt_display" class="form-control fw-bold" placeholder="Rp 0" onkeyup="formatRupiah(this)">
            </div>
            <div class="mb-4">
                <div class="preview-box" id="box_mkt" onclick="document.getElementById('f_mkt').click()">
                    <i class="bi bi-camera-fill fs-1 text-success"></i>
                </div>
                <input type="file" id="f_mkt" class="d-none" accept="image/*" capture="camera" onchange="handleFile(this, 'box_mkt')">
            </div>
            <button type="submit" class="btn btn-success w-100 py-3 fw-bold rounded-3 shadow">SIMPAN PROSPEK FUNDING</button>
        </form>
    </div>
</div>

<!-- BOTTOM NAV -->
<div class="bottom-nav">
    <a href="javascript:void(0)" id="nav_pageHome" class="nav-link active" onclick="showPage('pageHome')"><i class="bi bi-house-door"></i>HOME</a>
    <a href="javascript:void(0)" id="nav_pageColl" class="nav-link" onclick="showPage('pageColl')"><i class="bi bi-wallet2"></i>KOLEKSI</a>
    <a href="javascript:void(0)" id="nav_pageKredit" class="nav-link" onclick="showPage('pageKredit')"><i class="bi bi-cash-coin"></i>KREDIT</a>
    <a href="javascript:void(0)" id="nav_pageMkt" class="nav-link" onclick="showPage('pageMkt')"><i class="bi bi-person-plus"></i>FUNDING</a>
</div>

<script>
    const WEB_APP_URL = "GANTI_DENGAN_URL_DEPLOY_APPS_SCRIPT_ANDA"; 
    let currentPhoto = "";

    window.onload = () => {
        if (localStorage.getItem('isLoggedIn') === 'true') {
            document.getElementById('pageLogin').style.display = 'none';
            const u = JSON.parse(localStorage.getItem('userData'));
            document.getElementById('userGreet').innerText = `Halo, ${u.nama}`;
            syncData();
        }

        const sColl = document.getElementById('s_coll');
        if(sColl) {
            sColl.addEventListener('change', function() {
                document.getElementById('boxJanji').style.display = this.value === "Janji Bayar" ? 'block' : 'none';
                document.getElementById('boxNominal').style.display = this.value === "Rumah Kosong" ? 'none' : 'block';
            });
        }

        document.querySelectorAll('.select-prospek').forEach(el => {
            el.addEventListener('change', () => {
                const tab = document.getElementById('tab_mkt').value;
                const dep = document.getElementById('dep_mkt').value;
                document.getElementById('areaNominalMkt').style.display = 
                    (tab !== "Tidak Berminat" || dep !== "Tidak Berminat") ? 'block' : 'none';
            });
        });
    };

    document.getElementById('formLogin').onsubmit = function(e) {
        e.preventDefault();
        const user = document.getElementById('user').value;
        localStorage.setItem('isLoggedIn', 'true');
        localStorage.setItem('userData', JSON.stringify({nama: user}));
        location.reload();
    };

    async function syncData() {
        try {
            const res = await fetch(`${WEB_APP_URL}?action=init`).then(r => r.json());
            if(res.totalSetoran !== undefined) {
                document.getElementById('dashSetoran').innerText = `Rp ${Number(res.totalSetoran).toLocaleString('id-ID')}`;
                document.getElementById('barTarget').style.width = `${res.persenTarget || 0}%`;
                document.getElementById('txtPersen').innerText = `${res.persenTarget || 0}% dari Target`;
            }
            if(res.daftarNasabah) {
                document.getElementById('dataNasabah').innerHTML = res.daftarNasabah.map(n => `<option value="${n}">`).join('');
            }
        } catch (e) { console.log("Offline / Init Error"); }
    }

    async function submitForm(e, type) {
        e.preventDefault();
        if(!currentPhoto) return alert("Wajib Lampirkan Foto!");
        
        setLoading(true, "Mendapatkan Lokasi GPS...");
        navigator.geolocation.getCurrentPosition(async (pos) => {
            const user = JSON.parse(localStorage.getItem('userData'));
            let payload = {
                action: type,
                petugas: user.nama,
                gps: `${pos.coords.latitude},${pos.coords.longitude}`,
                foto: currentPhoto
            };

            if (type === 'SAVE_COLLECTION') {
                payload.nasabah = document.getElementById('n_coll').value;
                payload.status = document.getElementById('s_coll').value;
                payload.nominal = cleanRupiah(document.getElementById('nom_coll_display').value);
                payload.tglJanji = document.getElementById('tglJanji').value;
                payload.bertemu = document.getElementById('bertemu').value;
                payload.usaha = document.getElementById('usahaDebitur').value;
                payload.penyebab = document.getElementById('penyebab').value;
                payload.kendala = document.getElementById('kendala').value;
            } else if (type === 'SAVE_KREDIT') {
                payload.nasabah = document.getElementById('n_kredit').value;
                payload.wa = document.getElementById('wa_kredit').value;
                payload.plafon = cleanRupiah(document.getElementById('plafon_kredit').value);
                payload.tenor = document.getElementById('tenor_kredit').value;
                payload.tujuan = document.getElementById('tujuan_kredit').value;
                payload.alamat = document.getElementById('alamat_kredit').value;
                payload.kelurahan = document.getElementById('kel_kredit').value;
                payload.kota = document.getElementById('kota_kredit').value;
            } else if (type === 'SAVE_MARKETING') {
                payload.nasabah = document.getElementById('n_mkt').value;
                payload.wa = document.getElementById('wa_mkt').value;
                payload.alamat = document.getElementById('alamat_mkt').value;
                payload.kelurahan = document.getElementById('kel_mkt').value;
                payload.kota = document.getElementById('kota_mkt').value;
                payload.tabungan = document.getElementById('tab_mkt').value;
                payload.deposito = document.getElementById('dep_mkt').value;
                payload.nominal = cleanRupiah(document.getElementById('nom_mkt_display').value);
            }

            setLoading(true, "Mengirim Data ke Server...");
            try {
                await fetch(WEB_APP_URL, { 
                    method: 'POST', 
                    body: JSON.stringify(payload) 
                });
                alert("Data Berhasil Terkirim!");
                location.reload();
            } catch (err) {
                alert("Cek koneksi internet Anda!");
                setLoading(false);
            }
        }, () => { 
            alert("Gagal mendapatkan lokasi. Harap aktifkan GPS!"); 
            setLoading(false); 
        });
    }

    document.getElementById('formCollection').onsubmit = (e) => submitForm(e, 'SAVE_COLLECTION');
    document.getElementById('formKredit').onsubmit = (e) => submitForm(e, 'SAVE_KREDIT');
    document.getElementById('formMarketing').onsubmit = (e) => submitForm(e, 'SAVE_MARKETING');

    function formatRupiah(el) {
        let val = el.value.replace(/[^,\d]/g, '');
        let split = val.split(','), sisa = split[0].length % 3, rupiah = split[0].substr(0, sisa), ribuan = split[0].substr(sisa).match(/\d{3}/gi);
        if (ribuan) { rupiah += (sisa ? '.' : '') + ribuan.join('.'); }
        el.value = rupiah;
    }

    function cleanRupiah(val) { return parseInt(val.replace(/\./g, '')) || 0; }

    function showPage(id) {
        document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.querySelectorAll('.nav-link').forEach(n => n.classList.remove('active'));
        if(document.getElementById('nav_'+id)) document.getElementById('nav_'+id).classList.add('active');
        window.scrollTo(0,0);
    }

    function handleFile(input, boxId) {
        const file = input.files[0];
        if (!file) return;
        setLoading(true, "Kompresi Foto...");
        const reader = new FileReader();
        reader.onload = (e) => {
            const img = new Image();
            img.onload = () => {
                const canvas = document.createElement('canvas');
                const MAX_WIDTH = 800;
                let width = img.width, height = img.height;
                if (width > MAX_WIDTH) { height *= MAX_WIDTH / width; width = MAX_WIDTH; }
                canvas.width = width; canvas.height = height;
                canvas.getContext('2d').drawImage(img, 0, 0, width, height);
                currentPhoto = canvas.toDataURL('image/jpeg', 0.6);
                document.getElementById(boxId).innerHTML = `<img src="${currentPhoto}" class="rounded">`;
                setLoading(false);
            };
            img.src = e.target.result;
        };
        reader.readAsDataURL(file);
    }

    function setLoading(s, t) { 
        document.getElementById('loader').style.display = s ? 'flex' : 'none'; 
        document.getElementById('loaderText').innerText = t; 
    }

    function logout() { localStorage.clear(); location.reload(); }
</script>
</body>
</html>
