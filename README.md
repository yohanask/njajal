<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Tata Tertib Sekolah - SD Negeri Karangmangu 2025/2026</title>
<style>
  :root{
    --maxw: 800px;
    --text: #000;
  }
  body{
    font-family: "Times New Roman", serif;
    color: var(--text);
    margin: 20px;
    background: #fff;
  }
  header.top {
    display:flex;
    justify-content:space-between;
    align-items:center;
    margin-bottom:18px;
  }
  .btn {
    display:inline-block;
    padding:6px 12px;
    border-radius:6px;
    background:#333;
    color:#fff;
    text-decoration:none;
    font-size:14px;
  }
  .logo {
    display:block;
    margin: 0 auto 8px;
    width:100px;
    height:auto;
  }
  .school-id {
    text-align:center;
  }
  .school-id h2{
    margin:6px 0 2px;
    font-size:20px;
    letter-spacing:1px;
  }
  .school-id .muted{
    margin:2px 0;
    font-size:12px;
  }

  hr.sep{
    border:0;
    border-top:1px solid #222;
    margin:18px 0;
  }

  h1.title{
    font-size:18px;
    text-align:center;
    margin: 8px 0 14px;
    font-weight:bold;
    letter-spacing:1px;
  }
  .subtitle{
    text-align:center;
    margin-bottom:20px;
    font-size:13px;
    font-weight:600;
  }

  .content{
    max-width: var(--maxw);
    margin: 0 auto;
    text-align:justify;
    line-height:1.6;
    font-size:14px;
  }
  .section-title{
    margin-top:14px;
    margin-bottom:6px;
    font-weight:bold;
    text-transform:uppercase;
  }
  ol.main {
    margin-left: 20px;
    margin-right: 10px;
  }
  ol.main li { margin-bottom:8px; }
  .footer-note{
    margin-top:26px;
    border-left:4px solid #bbb;
    padding:10px 14px;
    background: #fafafa;
    font-size:13px;
    text-align:center;
    max-width: var(--maxw);
    margin-left:auto;
    margin-right:auto;
  }

  /* signature area */
  .signature-wrap{
    max-width: var(--maxw);
    margin: 40px auto 10px;
    position:relative;
    min-height:120px;
  }
  .sign-right{
    position:absolute;
    right:0;
    bottom:0;
    text-align:center;
    width:260px;
    font-size:14px;
  }
  .sign-right .name{ font-weight:bold; text-decoration:underline; margin-top:18px;}
  .sign-right .nip{ margin-top:6px; font-size:13px; }

  /* print layout */
  @media print{
    body { margin:10mm; }
    .btn { display:none; }
  }

  /* attempt to set F4 / folio page size when printing */
  @page {
    size: 210mm 330mm; /* F4 / Folio */
    margin: 15mm;
  }

  /* responsive smaller view */
  @media (max-width:600px){
    .content{ font-size:13px; padding:0 10px;}
    .sign-right{ width:200px; right:0; }
  }
</style>
</head>
<body>

<header class="top">
  <a href="javascript:history.back()" class="btn" title="Kembali ke Dashboard">⬅ Kembali ke Dashboard</a>
  <button id="printBtn" class="btn" title="Cetak / Simpan PDF">🖨 Cetak PDF</button>
</header>

<section class="school-id">
  <img src="https://iili.io/Fv15Laj.th.png" alt="Logo Sekolah" class="logo" />
  <h2>SD NEGERI KARANGMANGU</h2>
  <div class="muted">KORWILCAM DINDIK BATURRADEN<br/>KABUPATEN BANYUMAS</div>
</section>

<hr class="sep" />

<h1 class="title">TATA TERTIB SEKOLAH</h1>
<div class="subtitle">TAHUN PELAJARAN 2025/2026</div>

<main class="content" id="tataContent">
  <!-- content akan diisi lewat JS -->
  <div id="tataInner">
    <p style="text-align:center;">Memuat tata tertib...</p>
  </div>
</main>

<div class="footer-note" id="footerNote">
  Tata tertib ini berlaku bagi seluruh siswa SD Negeri Karangmangu Tahun Pelajaran 2025/2026 dan wajib dipatuhi demi terciptanya suasana belajar yang tertib, aman, dan nyaman.
</div>

<div class="signature-wrap">
  <div class="sign-right" id="signBlock">
    Banyumas, …………………………….. 2025<br/>
    Kepala Sekolah
    <div style="height:36px"></div>
    <div class="name">Indi Astuti, S.Pd.SD</div>
    <div class="nip" id="nipKS">NIP. -</div>
  </div>
</div>

<script>
/*
  tata-tertib.html
  - Mengambil data CSV publik dan memetakan berdasarkan header
  - Menggunakan parameter URL ?login=ADMIN (atau sesuai)
  - Header mapping dipakai: Login, Redirect, Nama, NIP, Kelas, NIPKS
*/

/* CONFIG: ganti URL ini jika perlu */
const SHEET_CSV_URL = "https://docs.google.com/spreadsheets/d/e/2PACX-1vSSdm3mxzcB1FU98NhzKAk9y4CEwokwUjQW2Yus9xDoXjHVPve0FS8zgSQ4AOGxV5J21nM9PybuYdfQ/pub?output=csv";

/* ambil param login dari URL */
function getLoginParam(){
  try{
    const p = new URLSearchParams(window.location.search).get('login') || '';
    return p.trim();
  }catch(e){ return ''; }
}

/* CSV parser sederhana yang mendukung quoted fields (koma di dalam field) */
function parseCSV(text){
  const rows = [];
  let cur = '';
  let row = [];
  let inQuotes = false;
  for (let i=0;i<text.length;i++){
    const ch = text[i];
    if (ch === '"') {
      // handle escaped ""
      if (inQuotes && text[i+1] === '"') { cur += '"'; i++; continue; }
      inQuotes = !inQuotes;
      continue;
    }
    if (ch === ',' && !inQuotes){
      row.push(cur);
      cur = '';
      continue;
    }
    if ((ch === '\n' || ch === '\r') && !inQuotes){
      // handle CRLF
      // if CRLF, skip next if it's LF
      if (ch === '\r' && text[i+1] === '\n'){ i++; }
      row.push(cur);
      rows.push(row);
      row = [];
      cur = '';
      continue;
    }
    cur += ch;
  }
  // leftover
  if (cur !== '' || row.length>0){
    row.push(cur);
    rows.push(row);
  }
  // trim whitespace in each cell
  return rows.map(r => r.map(c => c ? c.trim() : ''));
}

/* convert rows to array of objects using header row */
function rowsToObjects(rows){
  if (!rows || rows.length === 0) return [];
  const header = rows[0].map(h => h ? h.trim() : '');
  const data = [];
  for (let i=1;i<rows.length;i++){
    const r = rows[i];
    // skip empty rows
    if (r.every(cell => cell === '')) continue;
    const obj = {};
    for (let j=0;j<header.length;j++){
      const key = header[j] || `col${j}`;
      obj[key] = r[j] !== undefined ? r[j] : '';
    }
    data.push(obj);
  }
  return data;
}

/* fill tata tertib static content (the rules) */
function fillTataTertib(){
  const container = document.getElementById('tataInner');
  container.innerHTML = `
    <div>
      <div class="section-title">I. Kehadiran</div>
      <ol class="main">
        <li>Siswa wajib hadir 15 menit sebelum pelajaran dimulai.</li>
        <li>Siswa yang datang terlambat wajib lapor kepada guru piket.</li>
        <li>Ketidakhadiran karena sakit harus disertai surat keterangan orang tua; jika >3 hari disertai surat dokter.</li>
        <li>Ketidakhadiran tanpa keterangan dianggap alpa.</li>
      </ol>

      <div class="section-title">II. Pakaian dan Kerapian</div>
      <ol class="main">
        <li>Siswa wajib memakai seragam sekolah lengkap sesuai jadwal.</li>
        <li>Siswa wajib berpakaian rapi, sopan, dan bersih.</li>
        <li>Rambut siswa laki-laki dipotong rapi dan tidak dicat.</li>
        <li>Rambut siswa perempuan ditata rapi; bila panjang diikat atau dikepang.</li>
      </ol>

      <div class="section-title">III. Sikap dan Perilaku</div>
      <ol class="main">
        <li>Siswa wajib berdoa sebelum dan sesudah pembelajaran.</li>
        <li>Siswa wajib menghormati guru, karyawan, dan sesama siswa.</li>
        <li>Siswa wajib bersikap jujur, sopan, disiplin, dan bertanggung jawab.</li>
        <li>Siswa dilarang berkata kasar, berkelahi, atau melakukan tindakan yang mengganggu ketertiban.</li>
        <li>Siswa dilarang membawa barang berbahaya, narkoba, rokok, miras, atau sejenisnya.</li>
      </ol>

      <div class="section-title">IV. Kegiatan Belajar</div>
      <ol class="main">
        <li>Siswa wajib mengikuti seluruh kegiatan pembelajaran dengan tertib.</li>
        <li>Siswa wajib membawa perlengkapan belajar setiap hari.</li>
        <li>Siswa wajib mengumpulkan tugas sesuai tenggat waktu.</li>
        <li>Siswa wajib menjaga ketenangan kelas selama pembelajaran.</li>
        <li>Siswa dilarang mencontek dalam ulangan/ujian.</li>
      </ol>

      <div class="section-title">V. Kebersihan, Keamanan, dan Fasilitas</div>
      <ol class="main">
        <li>Siswa wajib menjaga kebersihan lingkungan sekolah.</li>
        <li>Buang sampah pada tempatnya dan ikut kegiatan kerja bakti.</li>
        <li>Menjaga fasilitas sekolah dan bertanggung jawab atas kerusakan yang disebabkan kelalaian.</li>
        <li>Dilarang mencoret-coret meja, kursi, dinding, atau fasilitas sekolah.</li>
      </ol>

      <div class="section-title">VI. Sanksi</div>
      <ol class="main">
        <li>Pelanggaran ringan: teguran lisan atau peringatan tertulis.</li>
        <li>Pelanggaran berulang: pemanggilan orang tua/wali.</li>
        <li>Pelanggaran berat: skorsing atau dikeluarkan dari sekolah sesuai kebijakan.</li>
      </ol>
    </div>
  `;
}

/* main: ambil CSV -> parse -> mapping -> isi NIPKS */
async function loadAndPopulate(){
  fillTataTertib();
  const loginKey = getLoginParam();
  if (!loginKey){
    // tidak ada login param -> hanya tampilkan tata tertib tanpa biodata
    document.getElementById('nipKS').textContent = "NIP. -";
    return;
  }
  try {
    const res = await fetch(SHEET_CSV_URL);
    if (!res.ok) throw new Error('Gagal mengambil sheet: '+res.status);
    const text = await res.text();
    const rows = parseCSV(text);
    const data = rowsToObjects(rows);
    // kolom header names are expected: Login, Redirect, Nama, NIP, Kelas, NIPKS
    // cari baris dengan Login === loginKey
    const found = data.find(r => {
      // tolerant: check exact and case-insensitive
      const val = (r['Login'] || r['login'] || r['LOGIN'] || '').toString().trim();
      return val === loginKey || val.toLowerCase() === loginKey.toLowerCase();
    });
    if (!found){
      // jika tidak ditemukan, isi NIPKS dari baris pertama fallback
      document.getElementById('nipKS').textContent = "NIP. -";
      return;
    }
    // ambil NIPKS (kolom F) — header bisa 'NIPKS'
    const nipks = found['NIPKS'] || found['Nipks'] || found['nipks'] || found['NIP KS'] || found['NIP.KS'] || found['NIP_KS'] || found['NIP Kepala Sekolah'] || '';
    // juga ambil nama guru/kls jika mau (tidak ditampilkan di dokumen utama)
    // masukkan NIPKS ke layout
    document.getElementById('nipKS').textContent = "NIP. " + (nipks || '-');
  } catch(err){
    console.error(err);
    document.getElementById('nipKS').textContent = "NIP. -";
  }
}

/* print button */
document.getElementById('printBtn').addEventListener('click', () => {
  window.print();
});

/* awal load */
loadAndPopulate();
</script>
</body>
</html>
