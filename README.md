/* Merapikan tabel agar enak dilihat di HP */
.table th { font-size: 0.75rem; text-transform: uppercase; letter-spacing: 0.5px; }
#tbodyMasterExcel tr td { vertical-align: middle; padding: 12px 8px; }

.amount-font { font-family: 'Consolas', monospace; font-weight: 600; font-size: 0.85rem; }
.tagihan-highlight { color: #d63031; background: #fff5f5; padding: 2px 5px; border-radius: 4px; }

/* Membatasi teks alamat agar tidak terlalu panjang (max 2 baris) */
.alamat-truncate {
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
    font-size: 0.75rem;
    line-height: 1.2;
    color: #636e72;
}

/* Tombol pilih nama agar lebih terlihat seperti link */
.btn-nama {
    text-align: left;
    font-weight: bold;
    color: var(--bkk-blue);
    text-decoration: none;
    padding: 0;
    border: none;
    background: none;
}
.btn-nama:hover { text-decoration: underline; }
