<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>BKK Mobile Collection - Pro</title>
    
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.1/font/bootstrap-icons.css">
    
    <style>
        :root { 
            --bkk-blue: #003366; 
            --bkk-blue-light: #0056b3;
            --bkk-gold: #FFD700; 
            --bg-light: #F1F5F9; 
        }
        
        body { background-color: var(--bg-light); font-family: 'Inter', sans-serif; padding-bottom: 90px; }

        /* Login */
        #loginOverlay { 
            position: fixed; inset: 0; background: linear-gradient(135deg, var(--bkk-blue) 0%, #001a33 100%); 
            z-index: 9999; display: flex; align-items: center; justify-content: center; 
        }
        .login-box { 
            background: white; width: 85%; max-width: 400px; border-radius: 20px; padding: 35px; 
            box-shadow: 0 15px 35px rgba(0,0,0,0.3); text-align: center; 
        }

        /* Dashboard */
        .header-app { 
            background: var(--bkk-blue); color: white; padding: 15px 20px; 
            border-bottom: 3px solid var(--bkk-gold); position: sticky; top: 0; z-index: 1000;
        }
        .stats-gradient {
            background: linear-gradient(135deg, var(--bkk-blue) 0%, var(--bkk-blue-light) 100%);
            color: white; border-radius: 20px; padding: 25px; box-shadow: 0 10px 20px rgba(0,51,102,0.2);
        }

        /* Cards */
        .card-custom { 
            background: white; border-radius: 15px; border: none; 
            box-shadow: 0 4px 6px -1px rgba(0,0,0,0.1); transition: 0.2s;
        }
        .card-custom:active { transform: scale(0.97); }

        /* Form */
        .btn-bkk { 
            background: var(--bkk-blue); color: white; border: none; 
            border-radius: 12px; padding: 15px; font-weight: 700; width: 100%; 
        }

        /* Bottom Nav */
        .bottom-nav { 
            position: fixed; bottom: 0; left: 0; right: 0; background: white; 
            display: flex; box-shadow: 0 -5px 15px rgba(0,0,0,0.05); z-index: 1000;
        }
        .nav-item { flex: 1; text-align: center; padding: 12px 0; color: #94A3B8; font-size: 0.7rem; font-weight: 600; }
        .nav-item i { font-size: 1.5rem; display: block; }
        .nav-item.active { color: var(--bkk-blue); }

        .page { display: none; animation: fadeIn 0.3s ease; }
        .page.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body>

<div id="loginOverlay">
    <div class="login-box">
        <h4 class="fw-bold mb-1" style="color: var(--bkk-blue)">BKK MOBILE</h4>
        <p class="text-muted small mb-4">Internal Collection System</p>
        <select id="selectPetugas" class="form-select mb-3 p-3 rounded-3"></select>
        <input type="password" id="inputPin" class="form-control mb-4 p-3 rounded-3 text-center" placeholder="PIN Keamanan" inputmode="numeric">
        <button onclick="handleLogin()" class="btn-bkk">MASUK</button>
    </div>
</div>

<div id="appContainer" style="display:none;">
    <div class="header-app d-flex justify-content-between align-items-center">
        <div>
            <small class="d-block opacity-75">BPR BKK JATENG</small>
            <span id="userNameDisp" class="fw-bold fs-5 text-uppercase">Petugas</span>
        </div>
        <button onclick="handleLogout()" class="btn btn-sm btn-outline-light rounded-pill">Keluar</button>
    </div>

    <div class="container mt-3">
        <div id="pageHome" class="page active">
            <div class="stats-gradient mb-4 text-center">
                <small class="opacity-75 d-block">Total Setoran Hari Ini</small>
                <h1 id="totalSetoran" class="fw-bold mb-3">Rp 0</h1>
                <div class="row border-top border-white border-opacity-25 pt-3">
                    <div class="col-6 border-end border-white border-opacity-25">
                        <small class="opacity-75 d-block">Target</small>
                        <h4 id="countTarget" class="fw-bold m-0">0</h4>
                    </div>
                    <div class="col-6">
                        <small class="opacity-75 d-block">Laporan</small>
                        <h4 id="countDone" class="fw-bold m-0">0</h4>
                    </div>
                </div>
            </div>

            <div class="row g-3">
                <div class="col-6" onclick="navigateTo('pageTugas', 'navTugas')">
                    <div class="card-custom p-4 text-center">
                        <i class="bi bi-list-stars fs-1 text-primary mb-2"></i>
                        <span class="d-block fw-bold small">Tugas Saya</span>
                    </div>
                </div>
                <div class="col-6" onclick="navigateTo('pageAdmin', 'navAdmin')">
                    <div class="card-custom p-4 text-center">
                        <i class="bi bi-clock-history fs-1 text-success mb-2"></i>
                        <span class="d-block fw-bold small">Riwayat</span>
                    </div>
                </div>
            </div>
        </div>

        <div id="pageTugas" class="page">
            <h6 class="fw-bold mb-3"><i class="bi bi-geo-fill"></i> Rencana Kunjungan</h6>
            <div id="listTugas"></div>
        </div>

        <div id="pageForm" class="page">
            <div class="card-custom p-4">
                <h5 class="fw-bold mb-4 text-primary" id="labelNasabah">Form Laporan</h5>
                <form id="formKoleksi">
                    <input type="hidden" id="inpNasabah">
                    <div class="mb-3">
                        <label class="small fw-bold">Status Kunjungan</label>
                        <select id="statusKunjungan" class="form-select p-3" onchange="cekStatus()" required>
                            <option value="Titip Angsuran">Titip Angsuran</option>
                            <option value="Bayar Sebagian">Bayar Sebagian</option>
                            <option value="Janji Bayar">Janji Bayar</option>
                            <option value="Rumah Kosong">Rumah Kosong</option>
                        </select>
                    </div>

                     <div class="col-6">
                        <label class="small fw-bold text-muted">Bertemu</label>
                        <select id="bertemuDengan" class="form-select py-2">
                            <option value="Debitur">Debitur</option>
                            <option value="Pasangan">Pasangan</option>
                            <option value="Keluarga">Keluarga</option>
                            <option value="Tetangga">Tetangga</option>
                        </select>
                    </div>
                </div>

                 <div id="areaNominal" class="p-3 border rounded bg-light mb-3 shadow-sm">
                    <div id="boxTglJanji">
                        <label class="small fw-bold text-danger">Tanggal Janji Bayar:</label>
                        <input type="date" id="tglJanjiBayar" class="form-control mb-2">
                    </div>
                    <label class="small fw-bold text-primary">Nominal Pembayaran (Rp):</label>
                    <input type="text" id="nominalDisplay" class="form-control fw-bold" placeholder="Rp 0" onkeyup="formatRupiah(this)">
                    <input type="hidden" id="nominalAsli">
                </div>

                <label class="small fw-bold">Jenis Usaha:</label>
                <select id="usahaDebitur" class="form-select mb-3" required>
                    <option value="" disabled selected>Pilih Usaha...</option>
                   <option value="Perdagangan">Perdagangan</option>
                            <option value="Pertanian dan Perkebunan">Pertanian dan Perkebunan</option>
                            <option value="Peternakan">Peternakan</option>
                            <option value="Jasa">Jasa</option>
                            <option value="Industri dan Produksi">Industri dan Produksi</option>
                            <option value="Properti dan Kontruksi">Properti dan Kontruksi</option>
                            <option value="Kuliner dan F&B">Kuliner dan F&B</option>
                            <option value="Transportasi dan Logistik">Transportasi dan Logistik</option>
                            <option value="Teknologi dan Kreatif">Teknologi dan Kreatif</option>
                            <option value="Keuangan dan Lainnya">Keuangan dan Lainnya</option>
                            <option value="Sosial dan Lainnya">Sosial dan Lainnya</option>
                            <option value="Perikanan dan Kelautan">Perikanan dan Kelautan</option>
                            <option value="Pariwisata dan Hiburan">Pariwisata dan Hiburan</option>
                            <option value="Pendidikan dan kesehatan">Pendidikan dan kesehatan</option>
                            <option value="Energi dan Lingkungan">Energi dan Lingkungan</option>
                </select>
              
              <select id="penyebab" class="form-select mb-2" required>
                            <option value="" disabled selected>Penyebab Tidak Bayar ...</option>
                            <option value="Karakter/Itikad Bayar Lemah">Karakter/Itikad Bayar Lemah</option>
                            <option value="Penggunaan Dana Tidak Sesuai">Penggunaan Dana Tidak Sesuai</option>
                            <option value="Dana Dipakai Oranglain">Dana Dipakai Oranglain</option>
                            <option value="Omset Menurun/Usaha Sepi">Omset Menurun/Usaha Sepi</option>
                            <option value="Manajemen Usaha Lemah">Manajemen Usaha Lemah</option>
                            <option value="Banyak Hutang">Banyak Hutang</option>
                            <option value="Sakit">Sakit</option>
                            <option value="Meninggal">Meninggal</option>
                            <option value="Perceraian">Perceraian</option>
                            <option value="Konflik Keluarga">Konflik Keluarga</option>
                            <option value="Modal Untuk Konsumsi Pribadi">Modal Untuk Konsumsi Pribadi</option>
                            <option value="Monitoring dan Penagihan Tidak Optimal">Monitoring dan Penagihan Tidak Optimal</option>
                            <option value="Piutang Tidak Terbayar">Piutang Tidak Terbayar</option>
                            <option value="Fraud Internal">Fraud Internal</option>
                            <option value="Penipuan Rekan Bisnis">Penipuan Rekan Bisnis</option>
                            <option value="SKIP">SKIP</option>
                            <option value="Bencana">Bencana</option>
                            <option value="Gagal Panen">Gagal Panen</option>
                            <option value="Persaingan Usaha">Persaingan Usaha</option>
                            <option value="Usaha Tutup">Usaha Tutup</option>
                        </select>

                        <select id="kendala" class="form-select mb-3" required>
                            <option value="" disabled selected>Kendala Penyelesaian ...</option>
                            <option value="Debitur Tidak Kooperatif">Debitur Tidak Kooperatif</option>
                            <option value="Nomor HP Tidak Aktif">Nomor HP Tidak Aktif</option>
                            <option value="Debitur Pindah Alamat">Debitur Pindah Alamat Tanpa Pemberitahuan</option>
                            <option value="Janji Palsu">Debitur Hanya Memberikan Janji Palsu</option>
                            <option value="Omset Menurun">Omset/Pendapatan Debitur Menurun Signifikan</option>
                            <option value="Bangkrut">Usaha Tutup/Bangkrut</option>
                            <option value="PHK">Debitur Mengalami PHK</option>
                            <option value="Sakit Berat">Debitur Sakit Berat/Meninggal</option>
                            <option value="Sengketa Agunan">Agunan Dalam Sengketa/Belum Balik Nama</option>
                        </select>

                    <div class="mb-4">
                        <label class="small fw-bold">Foto Bukti / Lokasi</label>
                        <input type="file" id="inpFile" class="form-control" accept="image/*" capture="camera" required>
                    </div>

                    <button type="submit" id="btnSubmit" class="btn-bkk shadow-lg">KIRIM DATA</button>
                    <button type="button" onclick="navigateTo('pageTugas', 'navTugas')" class="btn btn-link w-100 mt-2 text-muted text-decoration-none">Batal</button>
                </form>
            </div>
        </div>

        <div id="pageAdmin" class="page">
            <div class="d-flex justify-content-between mb-3 align-items-center">
                <h6 class="fw-bold m-0">Data Terkirim</h6>
                <button onclick="loadCloudHistory()" class="btn btn-sm btn-primary">Refresh</button>
            </div>
            <div id="cloudHistoryList"></div>
        </div>
    </div>

    <div class="bottom-nav">
        <div class="nav-item active" id="navHome" onclick="navigateTo('pageHome', 'navHome')">
            <i class="bi bi-house-door-fill"></i>Beranda
        </div>
        <div class="nav-item" id="navTugas" onclick="navigateTo('pageTugas', 'navTugas')">
            <i class="bi bi-clipboard2-data-fill"></i>Tugas
        </div>
        <div class="nav-item" id="navAdmin" onclick="navigateTo('pageAdmin', 'navAdmin')">
            <i class="bi bi-cloud-check-fill"></i>Riwayat
        </div>
    </div>
</div>

  
  
  
<script>
    // --- KONFIGURASI ---
    const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbyjm7J_BAxjoZ5WK6LoRU4NVZOiUq5su-TBxZe5r5kDh4tQz12A3J3lDIVjnVGeiZV8/exec";
    let activeUser = localStorage.getItem('bkk_user') || "";
    let photoBase64 = "";

    window.onload = () => { 
        if(activeUser) showMainApp(); 
        syncDatabase(); 
    };

    // --- LOGIKA DATA ---
    async function syncDatabase() {
        try {
            const [pRes, rRes] = await Promise.all([
                fetch(SCRIPT_URL + "?action=getPetugas").then(r => r.json()),
                fetch(SCRIPT_URL + "?action=getRencana").then(r => r.json())
            ]);
            localStorage.setItem('db_petugas', JSON.stringify(pRes.petugas));
            localStorage.setItem('db_rencana', JSON.stringify(rRes.rencana));
            renderPetugasSelect();
            if(activeUser) loadCloudHistory(); // Load awal untuk update dashboard
        } catch(e) { console.warn("Offline Mode"); }
    }

    function renderPetugasSelect() {
        const db = JSON.parse(localStorage.getItem('db_petugas')) || [];
        const el = document.getElementById('selectPetugas');
        el.innerHTML = '<option disabled selected>-- Pilih Nama Petugas --</option>';
        db.forEach(p => el.innerHTML += `<option value="${p.nama}">${p.nama}</option>`);
    }

    function handleLogin() {
        const name = document.getElementById('selectPetugas').value;
        const pin = document.getElementById('inputPin').value;
        const db = JSON.parse(localStorage.getItem('db_petugas')) || [];
        const user = db.find(u => u.nama === name && u.pin.toString() === pin);
        if(user) { 
            activeUser = name; 
            localStorage.setItem('bkk_user', name); 
            showMainApp(); 
            loadCloudHistory();
        } else { alert("PIN Salah!"); }
    }

    function showMainApp() {
        document.getElementById('loginOverlay').style.display = 'none';
        document.getElementById('appContainer').style.display = 'block';
        document.getElementById('userNameDisp').innerText = activeUser;
    }

    // --- DASHBOARD REKAP ---
    function updateDashboard(riwayat) {
        const dbRencana = JSON.parse(localStorage.getItem('db_rencana')) || [];
        const myTasks = dbRencana.filter(r => r.petugas === activeUser);
        document.getElementById('countTarget').innerText = myTasks.length;

        let totalUang = 0;
        let countDone = 0;
        const today = new Date().toLocaleDateString('id-ID');

        riwayat.forEach(h => {
            if (h.petugas === activeUser && h.waktu.includes(today)) {
                totalUang += parseInt(h.nominal || 0);
                countDone++;
            }
        });

        document.getElementById('countDone').innerText = countDone;
        document.getElementById('totalSetoran').innerText = 'Rp ' + totalUang.toLocaleString('id-ID');
    }

    // --- FORM LOGIC ---
    function navigateTo(pId, nId) {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.getElementById(pId).classList.add('active');
        document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
        document.getElementById(nId).classList.add('active');
        if(pId === 'pageTugas') renderTasks();
    }

    function formatRupiah(el) {
        let val = el.value.replace(/[^0-9]/g, '');
        el.value = val ? 'Rp ' + parseInt(val).toLocaleString('id-ID') : '';
    }

    function cekStatus() {
        const s = document.getElementById('statusKunjungan').value;
        document.getElementById('boxNominal').style.display = (s === 'Rumah Kosong' || s === 'Janji Bayar') ? 'none' : 'block';
    }

    document.getElementById('inpFile').onchange = function(e) {
        const reader = new FileReader();
        reader.onload = function(ev) {
            const img = new Image();
            img.src = ev.target.result;
            img.onload = function() {
                const canvas = document.createElement('canvas');
                const scale = 600 / img.width;
                canvas.width = 600; canvas.height = img.height * scale;
                canvas.getContext('2d').drawImage(img, 0, 0, 600, canvas.height);
                photoBase64 = canvas.toDataURL('image/jpeg', 0.7);
            }
        };
        reader.readAsDataURL(e.target.files[0]);
    };

    document.getElementById('formKoleksi').onsubmit = async function(e) {
        e.preventDefault();
        const btn = document.getElementById('btnSubmit');
        btn.disabled = true; btn.innerText = "Mengunci GPS & Mengirim...";

        let koordinat = "0,0";
        try {
            const pos = await new Promise((res, rej) => navigator.geolocation.getCurrentPosition(res, rej, {timeout: 5000}));
            koordinat = `${pos.coords.latitude},${pos.coords.longitude}`;
        } catch(err) { console.warn("GPS failed"); }

        const data = {
            petugas: activeUser,
            nama: document.getElementById('inpNasabah').value,
            status: document.getElementById('statusKunjungan').value,
            nominal: document.getElementById('nominalDisplay').value.replace(/[^0-9]/g, '') || 0,
            waktu: new Date().toLocaleString('id-ID'),
            foto: photoBase64,
            lokasi: koordinat
        };

        try {
            await fetch(SCRIPT_URL, { method: 'POST', mode: 'no-cors', body: JSON.stringify(data) });
            alert("Laporan Terkirim!"); 
            location.reload();
        } catch(err) {
            alert("Gagal Kirim ke Awan. Cek Sinyal!");
            btn.disabled = false; btn.innerText = "COBA LAGI";
        }
    };

    function renderTasks() {
        const db = JSON.parse(localStorage.getItem('db_rencana')) || [];
        const el = document.getElementById('listTugas');
        el.innerHTML = "";
        const myTasks = db.filter(r => r.petugas === activeUser);
        myTasks.forEach(t => {
            el.innerHTML += `
                <div class="card-custom p-3 mb-2 d-flex justify-content-between align-items-center" onclick="openForm('${t.nama}')">
                    <div><div class="fw-bold">${t.nama}</div><small class="text-muted">${t.alamat}</small></div>
                    <i class="bi bi-chevron-right text-primary"></i>
                </div>`;
        });
    }

    function openForm(nama) { 
        document.getElementById('inpNasabah').value = nama; 
        document.getElementById('labelNasabah').innerText = nama;
        navigateTo('pageForm', 'navTugas'); 
    }

    async function loadCloudHistory() {
        const el = document.getElementById('cloudHistoryList');
        el.innerHTML = '<p class="text-center p-4 small text-muted">Sinkronisasi...</p>';
        try {
            const res = await fetch(SCRIPT_URL + "?action=getRiwayat").then(r => r.json());
            el.innerHTML = "";
            updateDashboard(res.riwayat);
            res.riwayat.forEach(h => {
                const mapBtn = h.lokasi !== "0,0" ? `<a href="https://www.google.com/maps?q=${h.lokasi}" target="_blank" class="btn btn-sm btn-outline-danger py-0 px-2" style="font-size:0.7rem">GPS</a>` : '';
                el.innerHTML += `
                    <div class="card-custom mb-3 overflow-hidden">
                        <div class="row g-0">
                            <div class="col-4"><img src="${h.foto}" class="img-fluid h-100 w-100" style="object-fit:cover;min-height:90px"></div>
                            <div class="col-8 p-3">
                                <div class="d-flex justify-content-between">
                                    <span class="small fw-bold">${h.nama}</span>
                                    <small style="font-size:0.6rem">${h.waktu.split(' ')[0]}</small>
                                </div>
                                <div class="badge bg-light text-dark mb-2" style="font-size:0.6rem">${h.status}</div>
                                <div class="d-flex justify-content-between align-items-center">
                                    <span class="fw-bold text-success small">Rp${Number(h.nominal).toLocaleString('id-ID')}</span>
                                    ${mapBtn}
                                </div>
                            </div>
                        </div>
                    </div>`;
            });
        } catch(e) { el.innerHTML = '<p class="text-center p-4">Koneksi Sibuk.</p>'; }
    }

    function handleLogout() { localStorage.removeItem('bkk_user'); location.reload(); }
</script>
</body>
</html>
