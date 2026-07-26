<html lang="id" class="h-full">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gereja Kasih Karunia - Portal Pelayanan & Aksi Sosial</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Poppins -->
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Poppins', 'sans-serif'],
                    },
                    colors: {
                        primary: {
                            50: '#f0fdf4',
                            100: '#dcfce7',
                            500: '#22c55e',
                            600: '#16a34a',
                            700: '#15803d',
                            800: '#166534',
                            900: '#14532d',
                        }
                    }
                }
            }
        }
    </script>
    <style>
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        .conditional-field { display: none; }
        .conditional-field.show { display: block; }
        #auth-overlay { display: flex; }
        #auth-overlay.hidden-auth { display: none; }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 font-sans flex flex-col min-h-full">

    <!-- LOGIN / AUTHENTICATION OVERLAY -->
    <div id="auth-overlay" class="fixed inset-0 z-50 items-center justify-center bg-slate-900/80 backdrop-blur-md p-4">
        <div class="bg-white rounded-2xl max-w-md w-full p-8 shadow-2xl border border-slate-100">
            <div class="text-center mb-6">
                <div class="w-16 h-16 bg-emerald-600 text-white rounded-full flex items-center justify-center text-2xl mx-auto mb-3 shadow-lg">
                    <i class="fa-solid fa-lock"></i>
                </div>
                <h2 class="text-2xl font-bold text-slate-900">Autentikasi Masuk</h2>
                <p class="text-sm text-slate-500 mt-1">Masukkan ID Pengguna dan Kata Sandi untuk mengakses portal</p>
            </div>

            <form id="loginForm" onsubmit="handleLogin(event)" class="space-y-4">
                <div>
                    <label class="block text-sm font-semibold text-slate-700 mb-1">ID Pengguna</label>
                    <div class="relative">
                        <span class="absolute inset-y-0 left-0 pl-3.5 flex items-center text-slate-400"><i class="fa-solid fa-user-shield"></i></span>
                        <input type="text" id="loginId" required class="w-full pl-10 pr-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white text-sm" placeholder="Contoh: admin">
                    </div>
                </div>

                <div>
                    <label class="block text-sm font-semibold text-slate-700 mb-1">Kata Sandi (Password)</label>
                    <div class="relative">
                        <span class="absolute inset-y-0 left-0 pl-3.5 flex items-center text-slate-400"><i class="fa-solid fa-key"></i></span>
                        <input type="password" id="loginPass" required class="w-full pl-10 pr-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white text-sm" placeholder="••••••••">
                    </div>
                </div>

                <div id="loginError" class="hidden text-rose-500 text-xs font-medium text-center bg-rose-50 py-2 rounded-lg">
                    ID atau Kata Sandi salah. (Coba: ID: <b>admin</b>, Pass: <b>12345</b>)
                </div>

                <button type="submit" class="w-full py-3.5 bg-emerald-600 hover:bg-emerald-500 text-white rounded-xl font-semibold text-sm shadow-lg shadow-emerald-600/20 transition duration-200 mt-2">
                    Masuk ke Portal
                </button>
            </form>
            <div class="mt-4 text-center">
                <p class="text-xs text-slate-400">Default Login Demo — ID: <span class="font-semibold text-slate-600">admin</span> | Pass: <span class="font-semibold text-slate-600">12345</span></p>
            </div>
        </div>
    </div>

    <!-- Top Bar / Header -->
    <header class="bg-slate-900 text-white shadow-md sticky top-0 z-40">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex justify-between items-center h-20">
                <div class="flex items-center space-x-3 cursor-pointer" onclick="switchTab('beranda')">
                    <div class="w-12 h-12 bg-emerald-600 rounded-full flex items-center justify-center text-white text-xl font-bold shadow-lg">
                        <i class="fa-solid fa-church"></i>
                    </div>
                    <div>
                        <span class="text-xl font-bold tracking-wide block">Gereja Kasih Karunia</span>
                        <span class="text-xs text-emerald-400 tracking-wider uppercase font-medium">Portal Resmi & Layanan Umat</span>
                    </div>
                </div>
                
                <!-- Desktop Nav -->
                <nav class="hidden md:flex items-center space-x-2">
                    <button onclick="switchTab('beranda')" id="nav-beranda" class="nav-btn px-4 py-2 rounded-lg text-sm font-medium transition duration-200 bg-emerald-600 text-white shadow-sm">
                        <i class="fa-solid fa-house mr-2"></i> Beranda
                    </button>
                    <button onclick="switchTab('form')" id="nav-form" class="nav-btn px-4 py-2 rounded-lg text-sm font-medium transition duration-200 text-slate-300 hover:bg-slate-800 hover:text-white">
                        <i class="fa-solid fa-file-lines mr-2"></i> Formulir Layanan & Aksi
                    </button>
                    <button onclick="logout()" class="px-3 py-2 rounded-lg text-sm font-medium text-rose-400 hover:bg-rose-500/10 transition duration-200 ml-4" title="Keluar Akun">
                        <i class="fa-solid fa-right-from-bracket"></i> Keluar
                    </button>
                </nav>

                <!-- Mobile Menu Button -->
                <div class="md:hidden flex items-center space-x-2">
                    <button onclick="logout()" class="text-rose-400 p-2 text-xl" title="Keluar"><i class="fa-solid fa-right-from-bracket"></i></button>
                    <button id="mobile-menu-btn" class="text-slate-300 hover:text-white focus:outline-none text-2xl p-2">
                        <i class="fa-solid fa-bars"></i>
                    </button>
                </div>
            </div>
        </div>

        <!-- Mobile Nav Menu -->
        <div id="mobile-menu" class="hidden md:hidden bg-slate-800 border-t border-slate-700 px-4 pt-2 pb-4 space-y-1">
            <button onclick="switchTab('beranda'); toggleMobileMenu();" class="w-full text-left px-3 py-2 rounded-md text-base font-medium bg-emerald-600 text-white">Beranda</button>
            <button onclick="switchTab('form'); toggleMobileMenu();" class="w-full text-left px-3 py-2 rounded-md text-base font-medium text-slate-300 hover:bg-slate-700 hover:text-white">Isi Formulir</button>
            <button onclick="logout()" class="w-full text-left px-3 py-2 rounded-md text-base font-medium text-rose-400 hover:bg-slate-700">Keluar Sistem</button>
        </div>
    </header>

    <!-- Main Container -->
    <main class="flex-grow">

        <!-- TAB 1: BERANDA -->
        <section id="tab-beranda" class="tab-content active">
            <!-- Hero Banner -->
            <div class="relative bg-slate-900 text-white py-24 px-4 sm:px-6 lg:px-8 overflow-hidden">
                <div class="absolute inset-0 opacity-25 bg-[radial-gradient(#16a34a_1px,transparent_1px)] [background-size:16px_16px]"></div>
                <div class="relative max-w-4xl mx-auto text-center space-y-6">
                    <span class="inline-flex items-center gap-1.5 py-1.5 px-4 rounded-full text-xs font-semibold bg-emerald-500/10 text-emerald-400 border border-emerald-500/20">
                        <i class="fa-solid fa-hands-praying"></i> Selamat Datang di Rumah Tuhan
                    </span>
                    <h1 class="text-4xl sm:text-5xl lg:text-6xl font-extrabold tracking-tight">
                        Bertumbuh dalam Iman, <span class="text-emerald-400">Melayani dengan Kasih</span>
                    </h1>
                    <p class="text-lg sm:text-xl text-slate-300 max-w-2xl mx-auto font-light">
                        Portal terpadu pendataan jemaah dan pendaftaran aksi sosial donor darah Gereja Kasih Karunia.
                    </p>
                    <div class="pt-4">
                        <button onclick="switchTab('form')" class="inline-flex justify-center items-center gap-2 bg-emerald-600 hover:bg-emerald-500 text-white font-semibold px-8 py-4 rounded-xl shadow-lg transition duration-200">
                            <i class="fa-solid fa-pen-to-square"></i> Buka Formulir Pendaftaran & Pendataan
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <!-- TAB 2: FORMULIR TERPADU -->
        <section id="tab-form" class="tab-content py-12 px-4 sm:px-6 lg:px-8">
            <div class="max-w-3xl mx-auto">
                <div class="bg-white rounded-2xl shadow-xl border border-slate-100 overflow-hidden">
                    <div class="bg-emerald-700 px-6 py-5 text-white flex items-center justify-between">
                        <div>
                            <h2 class="text-xl font-bold">Formulir Terpadu Jemaah & Donor Darah</h2>
                            <p class="text-emerald-100 text-sm mt-0.5">Silakan pilih jenis keperluan Anda pada pilihan pertama.</p>
                        </div>
                        <div class="w-10 h-10 bg-white/10 rounded-lg flex items-center justify-center text-xl">
                            <i class="fa-solid fa-clipboard-list"></i>
                        </div>
                    </div>
                    
                    <form id="unifiedForm" class="p-6 sm:p-8 space-y-6" onsubmit="handleFormSubmit(event)">
                        <!-- Pilihan Utama -->
                        <div>
                            <label class="block text-sm font-semibold text-slate-700 mb-2">Tujuan Pengisian Formulir <span class="text-rose-500">*</span></label>
                            <select id="tujuanForm" required onchange="toggleFormFields()" class="w-full px-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white text-sm font-medium">
                                <option value="">-- Pilih Tujuan Pengisian --</option>
                                <option value="jemaah">Pendataan / Pembaruan Anggota Jemaah</option>
                                <option value="donor">Pendaftaran Aksi Donor Darah (Umum / Jemaah)</option>
                            </select>
                        </div>

                        <!-- Data Umum (Selalu Muncul) -->
                        <div class="space-y-6 border-t border-slate-100 pt-6">
                            <div>
                                <label class="block text-sm font-semibold text-slate-700 mb-2">Nama Lengkap <span class="text-rose-500">*</span></label>
                                <div class="relative">
                                    <span class="absolute inset-y-0 left-0 pl-3.5 flex items-center text-slate-400"><i class="fa-solid fa-user"></i></span>
                                    <input type="text" id="nama" required class="w-full pl-10 pr-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white text-sm" placeholder="Nama lengkap sesuai identitas">
                                </div>
                            </div>

                            <div class="grid grid-cols-1 sm:grid-cols-2 gap-6">
                                <div>
                                    <label class="block text-sm font-semibold text-slate-700 mb-2">Nomor WhatsApp / HP Aktif <span class="text-rose-500">*</span></label>
                                    <div class="relative">
                                        <span class="absolute inset-y-0 left-0 pl-3.5 flex items-center text-slate-400"><i class="fa-brands fa-whatsapp text-lg"></i></span>
                                        <input type="tel" id="whatsapp" required class="w-full pl-10 pr-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white text-sm" placeholder="08xxxxxxxxxx">
                                    </div>
                                </div>
                                <div>
                                    <label class="block text-sm font-semibold text-slate-700 mb-2">Alamat Domisili <span class="text-rose-500">*</span></label>
                                    <input type="text" id="alamat" required class="w-full px-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white text-sm" placeholder="Alamat tempat tinggal saat ini">
                                </div>
                            </div>

                            <!-- Tambahan Kolom Baru: Tanggal Lahir, Golongan Darah, Wilayah -->
                            <div class="grid grid-cols-1 sm:grid-cols-3 gap-6">
                                <div>
                                    <label class="block text-sm font-semibold text-slate-700 mb-2">Tanggal Lahir <span class="text-rose-500">*</span></label>
                                    <input type="date" id="tanggalLahirUmum" required class="w-full px-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white text-sm">
                                </div>
                                <div>
                                    <label class="block text-sm font-semibold text-slate-700 mb-2">Golongan Darah <span class="text-rose-500">*</span></label>
                                    <select id="golDarahUmum" required class="w-full px-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white text-sm">
                                        <option value="">Pilih Gol. Darah</option>
                                        <option value="A">A</option>
                                        <option value="B">B</option>
                                        <option value="AB">AB</option>
                                        <option value="O">O</option>
                                        <option value="Tidak Tahu">Belum Tahu</option>
                                    </select>
                                </div>
                                <div>
                                    <label class="block text-sm font-semibold text-slate-700 mb-2">Wilayah / Sektor <span class="text-rose-500">*</span></label>
                                    <input type="text" id="wilayahUmum" required class="w-full px-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white text-sm" placeholder="Contoh: Sektor 3 / Ungaran">
                                </div>
                            </div>
                        </div>

                        <!-- KONDISIONAL: Khusus Jemaah -->
                        <div id="sectionJemaah" class="conditional-field space-y-6 border-t border-slate-100 pt-6">
                            <div class="bg-emerald-50 p-4 rounded-xl text-xs text-emerald-800 font-medium">
                                <i class="fa-solid fa-circle-info mr-1"></i> Melengkapi data administratif tambahan warga jemaah Gereja Kasih Karunia.
                            </div>
                            <div class="grid grid-cols-1 sm:grid-cols-2 gap-6">
                                <div>
                                    <label class="block text-sm font-semibold text-slate-700 mb-2">Tempat Lahir</label>
                                    <input type="text" id="tempatLahir" class="w-full px-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white text-sm" placeholder="Kota Kelahiran">
                                </div>
                                <div>
                                    <label class="block text-sm font-semibold text-slate-700 mb-2">Jenis Kelamin</label>
                                    <select id="gender" class="w-full px-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white text-sm">
                                        <option value="">Pilih Jenis Kelamin</option>
                                        <option value="Laki-laki">Laki-laki</option>
                                        <option value="Perempuan">Perempuan</option>
                                    </select>
                                </div>
                            </div>
                        </div>

                        <!-- KONDISIONAL: Khusus Donor Darah -->
                        <div id="sectionDonor" class="conditional-field space-y-6 border-t border-slate-100 pt-6">
                            <div class="bg-rose-50 p-4 rounded-xl text-xs text-rose-800 font-medium">
                                <i class="fa-solid fa-heart-pulse mr-1"></i> Terbuka untuk jemaah maupun masyarakat umum. Syarat: Sehat, usia 17-60 tahun, berat min. 45 kg.
                            </div>
                            <div class="grid grid-cols-1 sm:grid-cols-2 gap-6">
                                <div>
                                    <label class="block text-sm font-semibold text-slate-700 mb-2">Nomor NIK / KTP</label>
                                    <input type="text" id="nik" class="w-full px-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white text-sm" placeholder="16 digit NIK">
                                </div>
                                <div>
                                    <label class="block text-sm font-semibold text-slate-700 mb-2">Status Anda Terhadap Gereja</label>
                                    <select id="statusKeanggotaan" class="w-full px-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white text-sm">
                                        <option value="">Pilih Status</option>
                                        <option value="Jemaah">Jemaah Gereja Kasih Karunia</option>
                                        <option value="Umum">Masyarakat Umum / Luar Gereja</option>
                                    </select>
                                </div>
                            </div>
                        </div>

                        <!-- Tombol Aksi -->
                        <div class="pt-4 flex gap-4">
                            <button type="button" onclick="switchTab('beranda')" class="w-1/3 bg-slate-100 hover:bg-slate-200 text-slate-700 font-semibold py-3.5 rounded-xl transition duration-200 text-sm">Batal</button>
                            <button type="submit" class="w-2/3 bg-emerald-600 hover:bg-emerald-500 text-white font-semibold py-3.5 rounded-xl shadow-lg shadow-emerald-600/20 transition duration-200 text-sm">Kirim Formulir</button>
                        </div>
                    </form>
                </div>
            </div>
        </section>

    </main>

    <!-- Footer -->
    <footer class="bg-slate-900 text-slate-400 py-10 mt-auto border-t border-slate-800">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 text-center space-y-4">
            <p class="text-sm">&copy; 2026 Gereja Kasih Karunia. Melayani dengan Kasih dan Ketulusan.</p>
        </div>
    </footer>

    <!-- Notification Modal -->
    <div id="successModal" class="hidden fixed inset-0 z-50 flex items-center justify-center bg-slate-900/60 backdrop-blur-sm p-4">
        <div class="bg-white rounded-2xl max-w-sm w-full p-6 text-center shadow-2xl">
            <div class="w-16 h-16 bg-emerald-100 text-emerald-600 rounded-full flex items-center justify-center text-3xl mx-auto mb-4">
                <i class="fa-solid fa-circle-check"></i>
            </div>
            <h3 id="modalTitle" class="text-lg font-bold text-slate-900 mb-2">Berhasil!</h3>
            <p id="modalMessage" class="text-sm text-slate-600 mb-6">Data Anda telah berhasil direkam.</p>
            <button onclick="closeModal()" class="w-full py-3 bg-slate-900 text-white rounded-xl font-medium text-sm hover:bg-slate-800 transition">Tutup</button>
        </div>
    </div>

    <!-- Script Logika -->
    <script>
        function handleLogin(e) {
            e.preventDefault();
            const idInput = document.getElementById('loginId').value.trim();
            const passInput = document.getElementById('loginPass').value.trim();
            const errorDiv = document.getElementById('loginError');

            if (idInput === 'admin' && passInput === '12345') {
                document.getElementById('auth-overlay').classList.add('hidden-auth');
                errorDiv.classList.add('hidden');
            } else {
                errorDiv.classList.remove('hidden');
            }
        }

        function logout() {
            document.getElementById('loginId').value = '';
            document.getElementById('loginPass').value = '';
            document.getElementById('loginError').classList.add('hidden');
            document.getElementById('auth-overlay').classList.remove('hidden-auth');
            switchTab('beranda');
        }

        function switchTab(tabName) {
            document.querySelectorAll('.tab-content').forEach(tab => tab.classList.remove('active'));
            document.getElementById('tab-' + tabName).classList.add('active');

            document.querySelectorAll('.nav-btn').forEach(btn => {
                btn.className = "nav-btn px-4 py-2 rounded-lg text-sm font-medium transition duration-200 text-slate-300 hover:bg-slate-800 hover:text-white";
            });
            const activeBtn = document.getElementById('nav-' + tabName);
            if(activeBtn) {
                activeBtn.className = "nav-btn px-4 py-2 rounded-lg text-sm font-medium transition duration-200 bg-emerald-600 text-white shadow-sm";
            }
            window.scrollTo({ top: 0, behavior: 'smooth' });
        }

        function toggleFormFields() {
            const val = document.getElementById('tujuanForm').value;
            const secJemaah = document.getElementById('sectionJemaah');
            const secDonor = document.getElementById('sectionDonor');

            // Reset requirement
            document.getElementById('tempatLahir').required = false;
            document.getElementById('gender').required = false;
            document.getElementById('nik').required = false;
            document.getElementById('statusKeanggotaan').required = false;

            secJemaah.classList.remove('show');
            secDonor.classList.remove('show');

            if(val === 'jemaah') {
                secJemaah.classList.add('show');
                document.getElementById('tempatLahir').required = true;
                document.getElementById('gender').required = true;
            } else if(val === 'donor') {
                secDonor.classList.add('show');
                document.getElementById('nik').required = true;
                document.getElementById('statusKeanggotaan').required = true;
            }
        }

        const mobileMenuBtn = document.getElementById('mobile-menu-btn');
        const mobileMenu = document.getElementById('mobile-menu');
        mobileMenuBtn.addEventListener('click', () => mobileMenu.classList.toggle('hidden'));
        function toggleMobileMenu() { mobileMenu.classList.add('hidden'); }

        function handleFormSubmit(e) {
            e.preventDefault();
            const tujuan = document.getElementById('tujuanForm').value;
            const msg = (tujuan === 'jemaah') 
                ? 'Terima kasih, data jemaah Anda berhasil dikirim dan akan diverifikasi sekretariat.' 
                : 'Pendaftaran donor darah berhasil! Panitia akan menghubungi Anda via WhatsApp.';
            
            document.getElementById('modalMessage').innerText = msg;
            document.getElementById('successModal').classList.remove('hidden');
            document.getElementById('unifiedForm').reset();
            toggleFormFields();
        }

        function closeModal() {
            document.getElementById('successModal').classList.add('hidden');
            switchTab('beranda');
        }
    </script>
</body>
</html>
