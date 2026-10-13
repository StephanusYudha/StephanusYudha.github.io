<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Monitoring Collection - BPR BKK Jateng (Perseroda)</title>
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
        .alarm-badge { animation: pulse-alarm 1.5s infinite; }
        @keyframes pulse-alarm {
            0% { opacity: 1; transform: scale(1); }
            50% { opacity: 0.6; transform: scale(1.05); }
            100% { opacity: 1; transform: scale(1); }
        }
    </style>
</head>
<body>

<div id="loginOverlay">
    <div class="card p-4 text-center shadow-lg" style="max-width: 380px; width: 90%;">
        <img src="https://bankbanjarharjo.id/assets/upload/images/logo.png" width="70" class="mx-auto mb-3">
        <h4 class="fw-bold">Akses Kolektor</h4>
        <p class="text-muted small">BPR BKK Jateng (Perseroda)</p>
        
        <!-- PILIH CABANG -->
        <select id="userCabang" class="form-select mb-2 text-center" onchange="filterPetugasByCabang()">
            <option value="" disabled selected>Memuat Cabang...</option>
        </select>

        <!-- PILIH PETUGAS -->
        <select id="userKolektor" class="form-select mb-2 text-center" disabled>
            <option value="" disabled selected>Pilih Cabang Dulu...</option>
        </select>
        
        <input type="password" id="passKolektor" class="form-control mb-3 text-center" placeholder="Password Unik">
        <button onclick="prosesLogin()" class="btn btn-primary w-100 fw-bold">MASUK</button>
    </div>
</div>

<div class="header-bank">
    <img src="https://bankbanjarharjo.id/assets/upload/images/logo.png" class="logo-bank">
    <div>
        <h4 class="fw-bold mb-0">BPR BKK JATENG (PERSERODA)</h4>
        <span id="labelPetugas" class="badge bg-warning text-dark">Petugas: -</span>
    </div>
    <div class="position-absolute top-0 end-0 m-2 d-flex gap-1">
        <a href="https://stephanusyudha.github.io/" target="_blank" class="btn btn-sm btn-outline-light" title="Portfolio Sistem"><i class="bi bi-globe"></i></a>
        <button onclick="logout()" class="btn btn-sm btn-outline-light" title="Keluar"><i class="bi bi-power"></i></button>
    </div>
</div>

<div class="container-fluid">
    <!-- PANEL ALARM OTOMATIS JATUH TEMPO & JANJI BAYAR -->
    <div id="panelAlarmContainer" class="card p-3 mb-4 border-danger bg-light" style="display: none;">
        <div class="d-flex justify-content-between align-items-center mb-2">
            <h6 class="fw-bold text-danger mb-0"><i class="bi bi-alarm-fill alarm-badge"></i> Peringatan Alarm Jatuh Tempo & Janji Bayar</h6>
            <span class="badge bg-danger" id="countAlarm">0 Agenda</span>
        </div>
        <p class="small text-muted mb-2">Nasabah berikut memiliki jadwal tagihan jatuh tempo hari ini/lewat atau jadwal janji bayar yang memerlukan tindak lanjut:</p>
        <div class="table-responsive bg-white rounded border" style="max-height: 200px; overflow-y: auto;">
            <table class="table table-sm table-striped mb-0">
                <thead class="table-danger small sticky-top">
                    <tr>
                        <th>Nasabah</th>
                        <th>Keterangan / Tenggat</th>
                        <th class="text-end">Nominal Tagihan</th>
                        <th>Status / Sumber</th>
                        <th class="text-center">Aksi / WA</th>
                    </tr>
                </thead>
                <tbody id="tbodyAlarm" class="small"></tbody>
            </table>
        </div>
    </div>

    <div class="card p-3 mb-4 border-success">
        <div class="d-flex justify-content-between align-items-center mb-3">
            <h6 class="fw-bold text-success mb-0"><i class="bi bi-database-check"></i> Rencana Kunjungan Anda</h6>
            <button onclick="tarikMasterCloud()" id="btnTarikCloud" class="btn btn-sm btn-success fw-bold">
                <i class="bi bi-cloud-download"></i> TARIK DATA PUSAT
            </button>
        </div>
        
        <div class="row g-2 mb-3">
            <div class="col-md-6">
                <label class="small fw-bold">Cari Nama/Alamat:</label>
                <input type="text" id="searchMaster" class="form-control form-control-sm" placeholder="Ketik nama nasabah atau alamat..." onkeyup="tampilkanMasterExcel()">
            </div>
            <div class="col-md-4">
                <label class="small fw-bold">Filter Tanggal:</label>
                <input type="date" id="filterTglMaster" class="form-control form-control-sm" onchange="tampilkanMasterExcel()">
            </div>
            <div class="col-md-2 text-end">
                <label class="d-none d-md-block">&nbsp;</label>
                <button onclick="hapusSemuaRencana()" class="btn btn-sm btn-danger w-100 fw-bold">BERSIHKAN CACHE</button>
            </div>
        </div>

        <div class="table-responsive sticky-table border rounded">
            <table class="table table-sm table-striped mb-0">
                <thead class="table-success small sticky-top">
                    <tr>
                        <th style="width: 25%">Nama</th>
                        <th style="width: 30%">Alamat</th>
                        <th style="width: 15%" class="text-end">OS</th>
                        <th style="width: 15%">Telp</th>
                        <th style="width: 15%" class="text-end">Tagihan</th>
                        <th style="width: 10%" class="text-center">Tgl</th>
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
                    <label class="small fw-bold">Nama Nasabah:</label>
                    <input type="text" id="namaNasabah" class="form-control mb-2" list="listNasabah" required oninput="autoFillData()" onchange="autoFillData()">
                    <datalist id="listNasabah"></datalist>

                    <label class="small fw-bold">Alamat:</label>
                    <textarea id="alamatNasabah" class="form-control mb-3 bg-light" rows="2" readonly></textarea>

                    <select id="bertemuDengan" class="form-select mb-3" required>
                        <option value="" disabled selected>Bertemu Dengan...</option>
                        <option value="Debitur">Debitur</option>
                        <option value="Suami">Suami</option>
                        <option value="Istri">Istri</option>
                        <option value="Keluarga">Keluarga</option>
                        <option value="Tetangga">Tetangga</option>
                        <option value="Tidak Ada Orang">Tidak Ada Orang</option>
                    </select>

                    <select id="statusKunjungan" class="form-select mb-3" required onchange="cekStatusJanji()">
                        <option value="" disabled selected>Status Penagihan...</option>
                        <option value="Janji Bayar">Janji Bayar</option>
                        <option value="Bayar Sebagian">Bayar Sebagian</option>
                        <option value="Titip Angsuran">Titip Angsuran</option>
                        <option value="Rumah Kosong">Rumah Kosong</option>
                    </select>

                    <div id="areaNominal" class="mb-3 p-2 border border-primary rounded shadow-sm" style="display: none;">
                        <label id="labelNominal" class="small fw-bold text-primary">NOMINAL (RP):</label>
                        <input type="text" id="nominalUang" class="form-control form-control-sm" placeholder="Contoh: 500.000" onkeyup="formatRupiah(this)">
                    </div>

                    <div id="areaJanjiBayar" class="mb-3 p-2 border border-danger rounded shadow-sm" style="display: none;">
                        <label class="small fw-bold text-danger">TANGGAL JANJI BAYAR:</label>
                        <input type="date" id="tglJanjiBayar" class="form-control form-control-sm">
                    </div>

                    <select id="hasilKunjungan" class="form-select mb-3" required>
                        <option value="" disabled selected>Hasil Kunjungan (Kode)...</option>
                        <option value="PTP">PTP (Janji Bayar)</option>
                        <option value="HPR">HPR (Hot Prospect)</option>
                        <option value="ALM">ALM (Almarhum)</option>
                        <option value="SKT">SKT (Sakit)</option>
                        <option value="KSS">KSS (Kena Kasus)</option>
                        <option value="BCN">BCN (Bencana)</option>
                        <option value="PBD">PBD (Menolak Bayar)</option>
                        <option value="JJA">JJA (Jual Aset)</option>
                        <option value="RES">RES (Restruktur)</option>
                        <option value="SKP">SKP (Skip / Kabur)</option>
                        <option value="CRA">CRA (Cerai)</option>
                        <option value="PDH">PDH (Pindah)</option>
                        <option value="SJA">SJA (Alamat Salah Sejak Awal)</option>
                        <option value="FRD">FRD (Fraud)</option>
                    </select> 

                    <textarea id="detailSolusi" class="form-control mb-2" rows="2" placeholder="Keterangan / Solusi..." required></textarea>
                    
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
                            <tr><th>Foto</th><th>Nasabah</th><th>Status</th></tr>
                        </thead>
                        <tbody id="tbodyLaporan" class="small"></tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
    const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbw12p9hKQNAMclmqhKFFIWbqXmIxEDTLki3_mEiAAWCcZTHRO88EHbCgj1LdviTfaS0/exec";
    
    let dataLaporan = JSON.parse(localStorage.getItem('laporan_bkk')) || [];
    let petugas = localStorage.getItem('petugas_aktif') || "";
    let cabang = localStorage.getItem('cabang_aktif') || "";
    let dataPetugas = JSON.parse(localStorage.getItem('data_petugas_cache')) || [];
    let base64Foto = "";

    window.onload = async () => {
        if(petugas && cabang) {
            loginSukses();
        } else {
            document.getElementById('loginOverlay').style.display = 'flex';
        }
        
        await tarikMasterCloud();
        getGPS();
        tampilkanData();
        cekAlarmJanjiBayar();
    };

    function isiDropdownPetugas() {
        const selectCabang = document.getElementById('userCabang');
        selectCabang.innerHTML = '<option value="" disabled selected>Pilih Kantor Cabang...</option>';
        
        // Ambil daftar cabang unik dari data petugas
        const listCabang = [...new Set(dataPetugas.map(p => p.cabang))];
        
        listCabang.forEach(c => {
            let opt = document.createElement('option');
            opt.value = c;
            opt.textContent = c;
            selectCabang.appendChild(opt);
        });

        // Jika cache cabang sudah tersimpan sebelumnya (misal saat reload), set ulang
        if(cabang) {
            selectCabang.value = cabang;
            filterPetugasByCabang();
            if(petugas) {
                document.getElementById('userKolektor').value = petugas;
            }
        }
    }

    function filterPetugasByCabang() {
        const selectedCabang = document.getElementById('userCabang').value;
        const selectPetugas = document.getElementById('userKolektor');
        
        selectPetugas.innerHTML = '<option value="" disabled selected>Pilih Nama Petugas...</option>';
        selectPetugas.disabled = false;
        
        const filteredPetugas = dataPetugas.filter(p => p.cabang.toLowerCase() === selectedCabang.toLowerCase());
        
        filteredPetugas.forEach(p => {
            let opt = document.createElement('option');
            opt.value = p.nama;
            opt.textContent = p.nama;
            selectPetugas.appendChild(opt);
        });
    }

    async function tarikMasterCloud() {
        const btn = document.getElementById('btnTarikCloud');
        if(btn) {
            btn.disabled = true;
            btn.innerHTML = '<span class="spinner-border spinner-border-sm"></span> Loading...';
        }

        try {
            const response = await fetch(SCRIPT_URL);
            const resCloud = await response.json();

            if (resCloud.master && Array.isArray(resCloud.master)) {
                localStorage.setItem('rencana_kunjungan', JSON.stringify(resCloud.master));
                tampilkanMasterExcel();
                updateDatalist();
            }

            if (resCloud.petugas && Array.isArray(resCloud.petugas) && resCloud.petugas.length > 0) {
                dataPetugas = resCloud.petugas;
                localStorage.setItem('data_petugas_cache', JSON.stringify(dataPetugas));
            }

            isiDropdownPetugas();
        } catch (err) {
            console.error("Gagal koneksi Cloud:", err);
            if(dataPetugas.length === 0) {
                dataPetugas = [{ cabang: "KCU Utama", nama: "Yudha Pratama", pass: "bkk2026" }];
            }
            isiDropdownPetugas();
        } finally {
            if(btn) {
                btn.disabled = false;
                btn.innerHTML = '<i class="bi bi-cloud-download"></i> TARIK DATA PUSAT';
            }
        }
    }

    function prosesLogin() {
        const selectedCabang = document.getElementById('userCabang').value;
        const selectedNama = document.getElementById('userKolektor').value;
        const inputPass = document.getElementById('passKolektor').value;

        if (!selectedCabang || !selectedNama) {
            alert("Silakan pilih Cabang dan Nama Petugas terlebih dahulu!");
            return;
        }

        const petugasDitemukan = dataPetugas.find(
            p => p.cabang.toLowerCase() === selectedCabang.toLowerCase() && 
                 p.nama.toLowerCase() === selectedNama.toLowerCase()
        );

        if (petugasDitemukan && inputPass === petugasDitemukan.pass.toString()) {
            localStorage.setItem('cabang_aktif', petugasDitemukan.cabang);
            localStorage.setItem('petugas_aktif', petugasDitemukan.nama);
            cabang = petugasDitemukan.cabang;
            petugas = petugasDitemukan.nama;
            loginSukses();
        } else {
            alert("Password salah untuk petugas " + selectedNama + "!");
        }
    }

    function loginSukses() {
        document.getElementById('loginOverlay').style.display = 'none';
        document.getElementById('labelPetugas').innerText = `Cabang: ${cabang} | Petugas: ${petugas}`;
        tampilkanMasterExcel();
        updateDatalist();
        tampilkanData();
        cekAlarmJanjiBayar();
    }

    function logout() { 
        localStorage.removeItem('petugas_aktif'); 
        localStorage.removeItem('cabang_aktif'); 
        location.reload(); 
    }

    function getGPS() {
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(pos => {
                document.getElementById('lokasiGps').value = `${pos.coords.latitude},${pos.coords.longitude}`;
            }, err => {
                console.warn("GPS Warning:", err.message);
                document.getElementById('lokasiGps').value = "-6.9666,110.4166";
            });
        }
    }

    function tampilkanMasterExcel() {
        const tbody = document.getElementById('tbodyMasterExcel');
        const search = document.getElementById('searchMaster').value.toLowerCase();
        const filterTgl = document.getElementById('filterTglMaster').value;
        const data = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
        
        tbody.innerHTML = "";
        
        const filtered = data.filter(i => {
            const matchPetugas = i.petugas.toLowerCase() === petugas.toLowerCase();
            const mS = i.nama.toLowerCase().includes(search) || i.alamat.toLowerCase().includes(search);
            const mD = filterTgl ? i.tanggal === filterTgl : true;
            return matchPetugas && mS && mD;
        });

        if(filtered.length === 0) {
            tbody.innerHTML = "<tr><td colspan='7' class='text-center py-3'>Tidak ada rencana kunjungan untuk Anda. Silakan klik Tarik Data Pusat.</td></tr>";
            return;
        }

        filtered.forEach(item => {
            const row = tbody.insertRow();
            const wa = item.telp.toString().replace(/^0/, '62').replace(/\D/g, '');
            row.innerHTML = `
                <td><button type="button" class="btn btn-sm btn-outline-primary py-0" onclick="pilihNasabah('${item.nama.replace(/'/g, "\\'")}')">${item.nama}</button></td>
                <td style="max-width:150px; overflow:hidden; text-overflow:ellipsis; white-space:nowrap">${item.alamat}</td>
                <td class="text-end">${Number(item.os).toLocaleString('id-ID')}</td>
                <td>${item.telp}</td>
                <td class="text-end fw-bold text-danger">${Number(item.tagihan).toLocaleString('id-ID')}</td>
                <td><span class="badge bg-secondary">${item.tanggal}</span></td>
                <td><a href="https://wa.me/${wa}" target="_blank" class="text-success"><i class="bi bi-whatsapp"></i></a></td>
            `;
        });
    }

    function updateDatalist() {
        const master = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
        const dl = document.getElementById('listNasabah');
        dl.innerHTML = "";
        master.filter(n => n.petugas.toLowerCase() === petugas.toLowerCase()).forEach(n => {
            let opt = document.createElement('option');
            opt.value = n.nama;
            dl.appendChild(opt);
        });
    }

    function autoFillData() {
        const nama = document.getElementById('namaNasabah').value;
        const master = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
        const ketemu = master.find(u => u.petugas.toLowerCase() === petugas.toLowerCase() && u.nama.toLowerCase() === nama.toLowerCase());
        
        if (ketemu) {
            document.getElementById('alamatNasabah').value = ketemu.alamat || "-";
        } else {
            document.getElementById('alamatNasabah').value = "";
        }
    }

    function pilihNasabah(nama) {
        document.getElementById('namaNasabah').value = nama;
        autoFillData();
        window.scrollTo({ top: document.getElementById('collectionForm').offsetTop - 20, behavior: 'smooth' });
    }

    function cekStatusJanji() {
        const status = document.getElementById('statusKunjungan').value;
        const areaJanji = document.getElementById('areaJanjiBayar');
        const areaNominal = document.getElementById('areaNominal');
        const labelNominal = document.getElementById('labelNominal');
        
        areaJanji.style.display = (status === "Janji Bayar") ? "block" : "none";

        if (status === "Janji Bayar" || status === "Bayar Sebagian" || status === "Titip Angsuran") {
            areaNominal.style.display = "block";
            if (status === "Janji Bayar") {
                labelNominal.innerText = "ESTIMASI NOMINAL JANJI BAYAR (RP):";
            } else if (status === "Bayar Sebagian") {
                labelNominal.innerText = "NOMINAL PEMBAYARAN SEBAGIAN (RP):";
            } else if (status === "Titip Angsuran") {
                labelNominal.innerText = "NOMINAL TITIP ANGSURAN (RP):";
            }
        } else {
            areaNominal.style.display = "none";
            document.getElementById('nominalUang').value = "";
        }
    }

    function formatRupiah(input) {
        let value = input.value.replace(/[^,\d]/g, '').toString();
        let split = value.split(',');
        let sisa = split[0].length % 3;
        let rupiah = split[0].substr(0, sisa);
        let ribuan = split[0].substr(sisa).match(/\d{3}/gi);

        if (ribuan) {
            let separator = sisa ? '.' : '';
            rupiah += separator + ribuan.join('.');
        }

        rupiah = split[1] != undefined ? rupiah + ',' + split[1] : rupiah;
        input.value = rupiah;
    }

    // --- SISTEM ALARM OTOMATIS: JATUH TEMPO & JANJI BAYAR ---
    function cekAlarmJanjiBayar() {
        const panelContainer = document.getElementById('panelAlarmContainer');
        const tbodyAlarm = document.getElementById('tbodyAlarm');
        const countAlarm = document.getElementById('countAlarm');
        
        tbodyAlarm.innerHTML = "";
        
        const todayStr = new Date().toLocaleDateString('en-CA'); 
        const todayDate = new Date(todayStr);
        let agendaList = [];

        // 1. Ambil otomatis dari Rencana Kunjungan Pusat (Berdasarkan Tanggal Tagihan / Jatuh Tempo)
        const masterData = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
        masterData.forEach(m => {
            if (m.petugas && m.petugas.toLowerCase() === petugas.toLowerCase() && m.tanggal) {
                const tglTempo = new Date(m.tanggal);
                const diffTime = tglTempo - todayDate;
                const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));

                if (diffDays <= 1) {
                    agendaList.push({
                        nama: m.nama,
                        tgl: m.tanggal,
                        diffDays: diffDays,
                        nominal: m.tagihan || "0",
                        keterangan: "Jatuh Tempo Tagihan",
                        telp: m.telp || ""
                    });
                }
            }
        });

        // 2. Ambil dari riwayat Janji Bayar Laporan Lapangan (PTP)
        dataLaporan.forEach(item => {
            if (item.petugas.toLowerCase() === petugas.toLowerCase() && item.status === "Janji Bayar" && item.janji_bayar && item.janji_bayar !== "-") {
                const janjiDate = new Date(item.janji_bayar);
                const diffTime = janjiDate - todayDate;
                const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));

                if (diffDays <= 1) {
                    const master = JSON.parse(localStorage.getItem('rencana_kunjungan')) || [];
                    const foundMaster = master.find(m => m.nama.toLowerCase() === item.nama.toLowerCase());
                    
                    agendaList.push({
                        nama: item.nama,
                        tgl: item.janji_bayar,
                        diffDays: diffDays,
                        nominal: item.nominal || "0",
                        keterangan: "Janji Bayar (PTP)",
                        telp: foundMaster ? foundMaster.telp : ""
                    });
                }
            }
        });

        if (agendaList.length > 0) {
            panelContainer.style.display = "block";
            countAlarm.innerText = agendaList.length + " Agenda";

            agendaList.forEach(agenda => {
                const row = tbodyAlarm.insertRow();
                const wa = agenda.telp ? agenda.telp.toString().replace(/^0/, '62').replace(/\D/g, '') : "";
                
                let badgeTime = "";
                if(agenda.diffDays < 0) badgeTime = `<span class="badge bg-dark text-danger">Terlambat ${Math.abs(agenda.diffDays)} Hari</span>`;
                else if(agenda.diffDays === 0) badgeTime = `<span class="badge bg-danger">Hari Ini!</span>`;
                else badgeTime = `<span class="badge bg-warning text-dark">Besok (H-1)</span>`;

                row.innerHTML = `
                    <td><b>${agenda.nama}</b><br><small class="text-muted">${agenda.keterangan}</small></td>
                    <td>${agenda.tgl}<br>${badgeTime}</td>
                    <td class="text-end fw-bold text-danger">Rp ${Number(agenda.nominal).toLocaleString('id-ID')}</td>
                    <td><span class="badge bg-secondary">${agenda.keterangan}</span></td>
                    <td class="text-center">
                        <button type="button" class="btn btn-sm btn-outline-primary py-0 mb-1 w-100" onclick="pilihNasabah('${agenda.nama.replace(/'/g, "\\'")}')" title="Kunjungi">Kunjungi</button>
                        ${wa ? `<a href="https://wa.me/${wa}?text=Halo\%20Bapak/Ibu\%20${encodeURIComponent(agenda.nama)},%20kami%20mengingatkan%20perihal%20kewajiban%20angsuran%20di%20BPR%20BKK%20Jateng." target="_blank" class="btn btn-sm btn-success py-0 w-100"><i class="bi bi-whatsapp"></i> Chat WA</a>` : ''}
                    </td>
                `;
            });
        } else {
            panelContainer.style.display = "none";
        }
    }

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
        if(this.files[0]) reader.readAsDataURL(this.files[0]);
    });

    document.getElementById('collectionForm').addEventListener('submit', async function(e) {
        e.preventDefault();
        const btn = document.getElementById('btnSimpan');
        
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(pos => {
                document.getElementById('lokasiGps').value = `${pos.coords.latitude},${pos.coords.longitude}`;
            });
        }

        const entri = {
            cabang: cabang,
            petugas: petugas,
            waktu: new Date().toLocaleString('id-ID'),
            nama: document.getElementById('namaNasabah').value,
            alamat: document.getElementById('alamatNasabah').value,
            bertemu: document.getElementById('bertemuDengan').value,
            status: document.getElementById('statusKunjungan').value,
            nominal: document.getElementById('nominalUang').value || "0",
            janji_bayar: document.getElementById('tglJanjiBayar').value || "-",
            hasil: document.getElementById('hasilKunjungan').value,
            solusi: document.getElementById('detailSolusi').value,
            gps: document.getElementById('lokasiGps').value || "-6.9666,110.4166",
            foto: base64Foto
        };

        dataLaporan.unshift(entri);
        localStorage.setItem('laporan_bkk', JSON.stringify(dataLaporan));
        tampilkanData();
        cekAlarmJanjiBayar();

        btn.disabled = true; 
        btn.innerText = "SINKRON...";

        try {
            await fetch(SCRIPT_URL, { 
                method: 'POST', 
                mode: 'no-cors', 
                headers: { 'Content-Type': 'text/plain;charset=utf-8' },
                body: JSON.stringify(entri) 
            });
            
            document.getElementById('syncStatus').innerHTML = "<span class='text-success fw-bold'>✓ Sinkron Berhasil ke Google Sheet</span>";
            this.reset();
            base64Foto = "";
            document.getElementById('preview-foto').style.display = 'none';
            document.getElementById('areaJanjiBayar').style.display = 'none';
            document.getElementById('areaNominal').style.display = 'none';
        } catch (err) {
            console.error(err);
            document.getElementById('syncStatus').innerHTML = "<span class='text-danger'>! Gagal Cloud (Tersimpan Lokal)</span>";
        }
        btn.disabled = false; 
        btn.innerText = "SIMPAN & SINKRON";
    });

    function tampilkanData() {
        const table = document.getElementById('tbodyLaporan');
        const search = document.getElementById('cariLaporan').value.toLowerCase();
        table.innerHTML = ""; let cb = 0;
        
        dataLaporan.forEach((item) => {
            if (item.nama.toLowerCase().includes(search)) {
                const isB = item.status.match(/Bayar|Titip/);
                if(isB) cb++;
                const row = table.insertRow();
                let infoNominal = (item.nominal && item.nominal !== "0") ? `<br><small class="text-primary fw-bold">Nominal: Rp ${item.nominal}</small>` : "";
                let infoJanji = (item.janji_bayar && item.janji_bayar !== "-") ? `<br><small class="text-danger">Janji Bayar: ${item.janji_bayar}</small>` : "";
                row.innerHTML = `
                    <td><img src="${item.foto}" class="img-table" onclick="window.open(this.src)"></td>
                    <td><b>${item.nama}</b><br><small class="text-muted">${item.waktu} (${item.petugas})</small>${infoNominal}${infoJanji}</td>
                    <td><span class="badge ${isB?'bg-success':'bg-warning text-dark'}">${item.status}</span></td>
                `;
            }
        });
        document.getElementById('countTotal').innerText = dataLaporan.length;
        document.getElementById('countBayar').innerText = cb;
        document.getElementById('countPending').innerText = dataLaporan.length - cb;
    }

    function hapusSemuaRencana() { 
        if(confirm("Bersihkan cache lokal rencana kunjungan?")) { 
            localStorage.removeItem('rencana_kunjungan'); 
            tampilkanMasterExcel(); 
            updateDatalist(); 
        } 
    }

    function exportToExcel() {
        const ws = XLSX.utils.json_to_sheet(dataLaporan.map(i => ({...i, foto: 'Lihat di Cloud'})));
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "Laporan");
        XLSX.writeFile(wb, `Laporan_Kolektor_${cabang}_${petugas}_${new Date().toLocaleDateString()}.xlsx`);
    }
</script>
</body>
</html>
