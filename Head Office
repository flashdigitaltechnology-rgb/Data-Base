<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>WiFi Bill Management - Ahmad Basyit</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
  * { margin:0; padding:0; box-sizing:border-box; font-family:'Segoe UI',Arial,sans-serif; }
  body { background:#f0f2f5; color:#333; }
  header { background:linear-gradient(135deg,#1e3c72,#2a5298); color:#fff; padding:20px 30px; display:flex; justify-content:space-between; align-items:center; }
  header h1 { font-size:22px; }
  header span { font-size:13px; opacity:.85; }
  nav { background:#fff; display:flex; gap:4px; padding:0 20px; box-shadow:0 2px 6px rgba(0,0,0,.08); overflow-x:auto; }
  nav button { background:none; border:none; padding:16px 20px; cursor:pointer; font-size:14px; font-weight:600; color:#666; border-bottom:3px solid transparent; white-space:nowrap; }
  nav button.active { color:#2a5298; border-bottom-color:#2a5298; }
  main { padding:24px; max-width:1200px; margin:auto; }
  .page { display:none; }
  .page.active { display:block; }
  .card { background:#fff; border-radius:12px; padding:24px; box-shadow:0 2px 12px rgba(0,0,0,.06); margin-bottom:20px; }
  .card h2 { font-size:18px; margin-bottom:16px; color:#1e3c72; border-left:4px solid #2a5298; padding-left:10px; }
  .grid { display:grid; grid-template-columns:repeat(auto-fit,minmax(220px,1fr)); gap:14px; }
  label { font-size:13px; font-weight:600; color:#555; display:block; margin-bottom:5px; }
  input, select, textarea { width:100%; padding:10px 12px; border:1px solid #ddd; border-radius:8px; font-size:14px; outline:none; transition:.2s; }
  input:focus, select:focus, textarea:focus { border-color:#2a5298; box-shadow:0 0 0 3px rgba(42,82,152,.1); }
  button.btn { background:#2a5298; color:#fff; border:none; padding:11px 20px; border-radius:8px; font-size:14px; font-weight:600; cursor:pointer; transition:.2s; }
  button.btn:hover { background:#1e3c72; }
  button.btn.green { background:#16a34a; } button.btn.green:hover { background:#15803d; }
  button.btn.red { background:#dc2626; } button.btn.red:hover { background:#b91c1c; }
  button.btn.gray { background:#6b7280; } button.btn.gray:hover { background:#4b5563; }
  table { width:100%; border-collapse:collapse; font-size:13px; }
  th, td { text-align:left; padding:10px 8px; border-bottom:1px solid #eee; }
  th { background:#f8fafc; color:#475569; font-weight:700; font-size:12px; text-transform:uppercase; }
  tr:hover { background:#f8fafc; }
  .badge { display:inline-block; padding:3px 10px; border-radius:20px; font-size:11px; font-weight:700; }
  .badge.ok { background:#dcfce7; color:#166534; }
  .badge.pending { background:#fef3c7; color:#92400e; }
  .badge.danger { background:#fee2e2; color:#991b1b; }
  .stat-grid { display:grid; grid-template-columns:repeat(auto-fit,minmax(200px,1fr)); gap:16px; margin-bottom:20px; }
  .stat { background:#fff; padding:20px; border-radius:12px; box-shadow:0 2px 10px rgba(0,0,0,.05); border-left:4px solid #2a5298; }
  .stat small { color:#64748b; font-size:12px; font-weight:600; }
  .stat h3 { font-size:24px; margin-top:6px; color:#1e3c72; }
  .modal-bg { display:none; position:fixed; inset:0; background:rgba(0,0,0,.5); z-index:99; align-items:center; justify-content:center; padding:20px; }
  .modal-bg.show { display:flex; }
  .modal { background:#fff; border-radius:12px; padding:24px; max-width:600px; width:100%; max-height:90vh; overflow:auto; }
  .toast { position:fixed; bottom:24px; right:24px; background:#16a34a; color:#fff; padding:14px 22px; border-radius:10px; font-weight:600; box-shadow:0 6px 20px rgba(0,0,0,.2); opacity:0; transform:translateY(20px); transition:.3s; z-index:999; }
  .toast.show { opacity:1; transform:translateY(0); }
  .flex { display:flex; gap:10px; flex-wrap:wrap; align-items:center; }
</style>
</head>
<body>

<header>
  <div>
    <h1>🌐 WiFi Bill Management</h1>
    <span>Ahmad Basyit — Sistem Penagihan Online</span>
  </div>
  <div style="text-align:right;font-size:13px">
    <div id="clock"></div>
  </div>
</header>

<nav>
  <button class="active" onclick="showPage('dashboard',this)">📊 Dashboard</button>
  <button onclick="showPage('pelanggan',this)">👥 Pelanggan</button>
  <button onclick="showPage('tagihan',this)">🧾 Tagihan</button>
  <button onclick="showPage('pembayaran',this)">💳 Pembayaran</button>
  <button onclick="showPage('income',this)">💰 Income</button>
  <button onclick="showPage('gangguan',this)">⚠️ Gangguan</button>
</nav>

<main>

<!-- DASHBOARD -->
<section id="dashboard" class="page active">
  <div class="stat-grid">
    <div class="stat"><small>Total Pelanggan</small><h3 id="statPelanggan">0</h3></div>
    <div class="stat" style="border-left-color:#16a34a"><small>Income Bulan Ini</small><h3 id="statIncome">Rp 0</h3></div>
    <div class="stat" style="border-left-color:#f59e0b"><small>Tagihan Belum Bayar</small><h3 id="statPending">0</h3></div>
    <div class="stat" style="border-left-color:#dc2626"><small>Laporan Gangguan</small><h3 id="statGangguan">0</h3></div>
  </div>
  <div class="card">
    <h2>Grafik Income Bulanan</h2>
    <canvas id="chartIncome" height="90"></canvas>
  </div>
</section>

<!-- PELANGGAN -->
<section id="pelanggan" class="page">
  <div class="card">
    <h2>Tambah Pelanggan WiFi</h2>
    <div class="grid">
      <div><label>Nama Lengkap</label><input id="p_nama" placeholder="Nama pelanggan"></div>
      <div><label>NIK</label><input id="p_nik" placeholder="16 digit NIK"></div>
      <div><label>Alamat</label><input id="p_alamat" placeholder="Alamat lengkap"></div>
      <div><label>Telepon</label><input id="p_telp" placeholder="08xxxxxxxxxx"></div>
      <div><label>Email</label><input id="p_email" type="email" placeholder="email@domain.com"></div>
      <div><label>Tanggal Pemasangan</label><input id="p_tgl" type="date"></div>
      <div><label>Dokumentasi (Foto)</label><input id="p_dok" type="file" accept="image/*"></div>
    </div>
    <div style="margin-top:16px"><button class="btn" onclick="tambahPelanggan()">➕ Simpan Pelanggan</button></div>
  </div>

  <div class="card">
    <h2>Daftar Pelanggan</h2>
    <div style="overflow-x:auto">
      <table>
        <thead><tr><th>#</th><th>Nama</th><th>NIK</th><th>Alamat</th><th>Telp</th><th>Email</th><th>Tgl Pasang</th><th>Dokumentasi</th><th>Aksi</th></tr></thead>
        <tbody id="tblPelanggan"></tbody>
      </table>
    </div>
  </div>
</section>

<!-- TAGIHAN -->
<section id="tagihan" class="page">
  <div class="card">
    <h2>Generate Tagihan Bulanan</h2>
    <div class="grid">
      <div><label>Pilih Pelanggan</label><select id="t_pelanggan"></select></div>
      <div><label>Bulan</label><input id="t_bulan" type="month"></div>
      <div><label>Nominal (Rp)</label><input id="t_nominal" type="number" value="150000"></div>
    </div>
    <div style="margin-top:16px"><button class="btn" onclick="generateTagihan()">🧾 Buat Tagihan & PDF</button></div>
  </div>

  <div class="card">
    <h2>Daftar Tagihan</h2>
    <div style="overflow-x:auto">
      <table>
        <thead><tr><th>#</th><th>Pelanggan</th><th>Periode</th><th>Nominal</th><th>Status</th><th>Aksi</th></tr></thead>
        <tbody id="tblTagihan"></tbody>
      </table>
    </div>
  </div>
</section>

<!-- PEMBAYARAN -->
<section id="pembayaran" class="page">
  <div class="card">
    <h2>Informasi Rekening Transfer</h2>
    <div class="grid">
      <div style="background:#f8fafc;padding:16px;border-radius:10px;border-left:4px solid #1e3c72">
        <strong>🏦 Bank Mandiri</strong><br>
        No. Rek: <b>1640006614236</b><br>
        A/N: <b>AHMAD BASYIT</b>
      </div>
      <div style="background:#f8fafc;padding:16px;border-radius:10px;border-left:4px solid #dc2626">
        <strong>🏦 Bank BCA</strong><br>
        No. Rek: <b>4730892069</b><br>
        A/N: <b>AHMAD BASYIT</b>
      </div>
    </div>
  </div>

  <div class="card">
    <h2>Konfirmasi Pembayaran</h2>
    <div class="grid">
      <div><label>Pilih Tagihan</label><select id="pay_tagihan"></select></div>
      <div><label>Metode</label>
        <select id="pay_metode">
          <option>Bank Mandiri</option>
          <option>Bank BCA</option>
        </select>
      </div>
      <div><label>Nama Pengirim</label><input id="pay_nama" placeholder="Nama pemilik rekening"></div>
      <div><label>Bukti Transfer (Opsional)</label><input id="pay_bukti" type="file" accept="image/*"></div>
    </div>
    <div style="margin-top:16px"><button class="btn green" onclick="konfirmasiPembayaran()">✅ Konfirmasi Sudah Bayar</button></div>
  </div>

  <div class="card">
    <h2>Riwayat Transaksi Berhasil</h2>
    <div style="overflow-x:auto">
      <table>
        <thead><tr><th>#</th><th>Pelanggan</th><th>Periode</th><th>Nominal</th><th>Metode</th><th>Waktu</th></tr></thead>
        <tbody id="tblTransaksi"></tbody>
      </table>
    </div>
  </div>
</section>

<!-- INCOME -->
<section id="income" class="page">
  <div class="stat-grid">
    <div class="stat"><small>Income Bulan Ini</small><h3 id="incBulanIni">Rp 0</h3></div>
    <div class="stat" style="border-left-color:#16a34a"><small>Income Tahun Ini</small><h3 id="incTahunIni">Rp 0</h3></div>
    <div class="stat" style="border-left-color:#f59e0b"><small>Total Transaksi</small><h3 id="incTotal">0</h3></div>
  </div>
  <div class="card">
    <h2>Laporan Income per Bulan</h2>
    <div style="overflow-x:auto">
      <table>
        <thead><tr><th>Periode</th><th>Jumlah Transaksi</th><th>Total Income</th></tr></thead>
        <tbody id="tblIncome"></tbody>
      </table>
    </div>
  </div>
</section>

<!-- GANGGUAN -->
<section id="gangguan" class="page">
  <div class="card">
    <h2>Laporkan Gangguan</h2>
    <div class="grid">
      <div><label>Pelanggan</label><select id="g_pelanggan"></select></div>
      <div><label>Jenis Gangguan</label>
        <select id="g_jenis">
          <option>Internet Lambat</option>
          <option>Koneksi Putus</option>
          <option>Router Mati</option>
          <option>Lainnya</option>
        </select>
      </div>
      <div><label>Tanggal Lapor</label><input id="g_tgl" type="date"></div>
      <div style="grid-column:1/-1"><label>Deskripsi</label><textarea id="g_desk" rows="3" placeholder="Jelaskan detail gangguan..."></textarea></div>
    </div>
    <div style="margin-top:16px"><button class="btn red" onclick="tambahGangguan()">⚠️ Kirim Laporan</button></div>
  </div>

  <div class="card">
    <h2>Daftar Laporan Gangguan</h2>
    <div style="overflow-x:auto">
      <table>
        <thead><tr><th>#</th><th>Pelanggan</th><th>Jenis</th><th>Deskripsi</th><th>Tanggal</th><th>Status</th><th>Aksi</th></tr></thead>
        <tbody id="tblGangguan"></tbody>
      </table>
    </div>
  </div>
</section>

</main>

<!-- MODAL PREVIEW PDF -->
<div class="modal-bg" id="modal">
  <div class="modal">
    <div class="flex" style="justify-content:space-between;margin-bottom:16px">
      <h2 style="font-size:18px;color:#1e3c72">Preview Tagihan</h2>
      <button class="btn gray" onclick="document.getElementById('modal').classList.remove('show')">Tutup</button>
    </div>
    <div id="modalContent"></div>
  </div>
</div>

<div class="toast" id="toast"></div>

<script>
/* ================= STORAGE ================= */
const DB = {
  get: k => JSON.parse(localStorage.getItem('wifi_'+k) || '[]'),
  set: (k,v) => localStorage.setItem('wifi_'+k, JSON.stringify(v))
};

/* ================= NAV ================= */
function showPage(id, btn){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  if(btn) btn.classList.add('active');
  if(id==='dashboard') renderDashboard();
  if(id==='pelanggan') renderPelanggan();
  if(id==='tagihan') { renderTagihan(); fillSelectPelanggan(); }
  if(id==='pembayaran') { renderPembayaran(); fillSelectTagihan(); }
  if(id==='income') renderIncome();
  if(id==='gangguan') { renderGangguan(); fillSelectPelanggan(); }
}

/* ================= TOAST ================= */
function toast(msg, color='#16a34a'){
  const t = document.getElementById('toast');
  t.textContent = msg; t.style.background = color;
  t.classList.add('show');
  setTimeout(()=>t.classList.remove('show'), 2800);
}

/* ================= PELANGGAN ================= */
function tambahPelanggan(){
  const nama = p_nama.value.trim();
  const nik = p_nik.value.trim();
  if(!nama || !nik) return toast('Nama dan NIK wajib diisi!','#dc2626');

  const file = p_dok.files[0];
  const reader = new FileReader();
  reader.onload = e => {
    const data = DB.get('pelanggan');
    data.push({
      id: 'P' + Date.now(),
      nama, nik,
      alamat: p_alamat.value,
      telp: p_telp.value,
      email: p_email.value,
      tgl: p_tgl.value,
      dok: e.target.result || ''
    });
    DB.set('pelanggan', data);
    ['p_nama','p_nik','p_alamat','p_telp','p_email','p_tgl','p_dok'].forEach(id=>document.getElementById(id).value='');
    renderPelanggan(); toast('Pelanggan berhasil ditambahkan!');
  };
  if(file) reader.readAsDataURL(file); else reader.onload({target:{result:''}});
}

function renderPelanggan(){
  const data = DB.get('pelanggan');
  const tbl = document.getElementById('tblPelanggan');
  if(!data.length){ tbl.innerHTML = '<tr><td colspan="9" style="text-align:center;color:#999;padding:20px">Belum ada pelanggan</td></tr>'; return; }
  tbl.innerHTML = data.map((p,i)=>`
    <tr>
      <td>${i+1}</td><td><b>${p.nama}</b></td><td>${p.nik}</td><td>${p.alamat||'-'}</td>
      <td>${p.telp||'-'}</td><td>${p.email||'-'}</td><td>${p.tgl||'-'}</td>
      <td>${p.dok?`<img src="${p.dok}" style="width:40px;height:40px;object-fit:cover;border-radius:6px">`:'-'}</td>
      <td><button class="btn red" style="padding:6px 10px;font-size:12px" onclick="hapusPelanggan('${p.id}')">Hapus</button></td>
    </tr>`).join('');
}

function hapusPelanggan(id){
  if(!confirm('Hapus pelanggan ini?')) return;
  DB.set('pelanggan', DB.get('pelanggan').filter(p=>p.id!==id));
  renderPelanggan(); toast('Pelanggan dihapus','#dc2626');
}

/* ================= TAGIHAN ================= */
function fillSelectPelanggan(){
  const opts = DB.get('pelanggan').map(p=>`<option value="${p.id}">${p.nama}</option>`).join('');
  ['t_pelanggan','g_pelanggan'].forEach(id=>{
    const el = document.getElementById(id); if(el) el.innerHTML = opts;
  });
}

function generateTagihan(){
  const pid = t_pelanggan.value;
  const bulan = t_bulan.value;
  const nominal = parseInt(t_nominal.value);
  if(!pid || !bulan || !nominal) return toast('Lengkapi semua data!','#dc2626');

  const pel = DB.get('pelanggan').find(p=>p.id===pid);
  const data = DB.get('tagihan');
  // Cek duplikat
  if(data.find(t=>t.pid===pid && t.bulan===bulan)) return toast('Tagihan periode ini sudah ada!','#f59e0b');

  const tag = {
    id: 'T' + Date.now(),
    pid, nama: pel.nama, bulan, nominal,
    status: 'pending',
    tglBuat: new Date().toISOString()
  };
  data.push(tag);
  DB.set('tagihan', data);
  renderTagihan();
  buatPDF(tag);
  toast('Tagihan dibuat!');
}

function renderTagihan(){
  const data = DB.get('tagihan').reverse();
  const tbl = document.getElementById('tblTagihan');
  if(!data.length){ tbl.innerHTML = '<tr><td colspan="6" style="text-align:center;color:#999;padding:20px">Belum ada tagihan</td></tr>'; return; }
  tbl.innerHTML = data.map((t,i)=>`
    <tr>
      <td>${i+1}</td><td><b>${t.nama}</b></td><td>${t.bulan}</td>
      <td>Rp ${t.nominal.toLocaleString('id-ID')}</td>
      <td>${t.status==='paid'?'<span class="badge ok">Lunas</span>':'<span class="badge pending">Belum Bayar</span>'}</td>
      <td>
        <button class="btn" style="padding:6px 10px;font-size:12px" onclick="previewPDF('${t.id}')">📄 PDF</button>
        ${t.status!=='paid'?`<button class="btn red" style="padding:6px 10px;font-size:12px" onclick="hapusTagihan('${t.id}')">Hapus</button>`:''}
      </td>
    </tr>`).join('');
}

function hapusTagihan(id){
  if(!confirm('Hapus tagihan ini?')) return;
  DB.set('tagihan', DB.get('tagihan').filter(t=>t.id!==id));
  renderTagihan(); toast('Tagihan dihapus','#dc2626');
}

/* ================= PDF ================= */
function buatPDF(tag){
  const { jsPDF } = window.jspdf;
  const doc = new jsPDF();
  const pel = DB.get('pelanggan').find(p=>p.id===tag.pid) || {};

  doc.setFillColor(30,60,114); doc.rect(0,0,210,35,'F');
  doc.setTextColor(255); doc.setFontSize(18); doc.text('TAGIHAN INTERNET WIFI', 105, 15, {align:'center'});
  doc.setFontSize(10); doc.text('Ahmad Basyit — WiFi Bill Management', 105, 25, {align:'center'});

  doc.setTextColor(0); doc.setFontSize(11);
  let y = 50;
  doc.text('No. Tagihan : ' + tag.id, 20, y); y+=7;
  doc.text('Nama        : ' + (pel.nama||tag.nama), 20, y); y+=7;
  doc.text('NIK         : ' + (pel.nik||'-'), 20, y); y+=7;
  doc.text('Alamat      : ' + (pel.alamat||'-'), 20, y); y+=7;
  doc.text('Telepon     : ' + (pel.telp||'-'), 20, y); y+=7;
  doc.text('Periode     : ' + tag.bulan, 20, y); y+=7;
  doc.text('Status      : ' + (tag.status==='paid'?'LUNAS':'BELUM BAYAR'), 20, y);

  y += 15;
  doc.setFillColor(240,242,245); doc.rect(20,y-6,170,18,'F');
  doc.setFontSize(12); doc.setFont(undefined,'bold');
  doc.text('TOTAL TAGIHAN', 25, y+5);
  doc.text('Rp ' + tag.nominal.toLocaleString('id-ID'), 185, y+5, {align:'right'});
  doc.setFont(undefined,'normal');

  y += 30;
  doc.setFontSize(11); doc.text('Silakan transfer ke:', 20, y); y+=7;
  doc.text('Bank Mandiri : 1640006614236 (A/N AHMAD BASYIT)', 20, y); y+=7;
  doc.text('Bank BCA     : 4730892069 (A/N AHMAD BASYIT)', 20, y); y+=12;
  doc.setFontSize(9); doc.setTextColor(120);
  doc.text('Harap konfirmasi pembayaran melalui website setelah transfer.', 20, y);
  doc.text('Terima kasih telah berlangganan.', 20, y+6);

  doc.save(`Tagihan_${tag.nama}_${tag.bulan}.pdf`);
}

function previewPDF(id){
  const tag = DB.get('tagihan').find(t=>t.id===id);
  const pel = DB.get('pelanggan').find(p=>p.id===tag.pid) || {};
  document.getElementById('modalContent').innerHTML = `
    <div style="background:#1e3c72;color:#fff;padding:16px;border-radius:8px;margin-bottom:16px">
      <h3>TAGIHAN INTERNET WIFI</h3>
      <small>Ahmad Basyit — WiFi Bill Management</small>
    </div>
    <table style="font-size:13px">
      <tr><td><b>No. Tagihan</b></td><td>${tag.id}</td></tr>
      <tr><td><b>Nama</b></td><td>${pel.nama||tag.nama}</td></tr>
      <tr><td><b>NIK</b></td><td>${pel.nik||'-'}</td></tr>
      <tr><td><b>Alamat</b></td><td>${pel.alamat||'-'}</td></tr>
      <tr><td><b>Telepon</b></td><td>${pel.telp||'-'}</td></tr>
      <tr><td><b>Periode</b></td><td>${tag.bulan}</td></tr>
      <tr><td><b>Status</b></td><td>${tag.status==='paid'?'<span class="badge ok">LUNAS</span>':'<span class="badge pending">BELUM BAYAR</span>'}</td></tr>
      <tr style="background:#f0f2f5"><td><b>TOTAL</b></td><td><b>Rp ${tag.nominal.toLocaleString('id-ID')}</b></td></tr>
    </table>
    <div style="margin-top:16px;padding:12px;background:#f8fafc;border-radius:8px;font-size:13px">
      <b>Transfer ke:</b><br>
      Bank Mandiri: 1640006614236 (A/N AHMAD BASYIT)<br>
      Bank BCA: 4730892069 (A/N AHMAD BASYIT)
    </div>
    <div style="margin-top:16px"><button class="btn" onclick="buatPDF(JSON.parse('${JSON.stringify(tag).replace(/'/g,"\\'")}'))">⬇️ Download PDF</button></div>
  `;
  document.getElementById('modal').classList.add('show');
}

/* ================= PEMBAYARAN ================= */
function fillSelectTagihan(){
  const opts = DB.get('tagihan').filter(t=>t.status!=='paid')
    .map(t=>`<option value="${t.id}">${t.nama} — ${t.bulan} — Rp ${t.nominal.toLocaleString('id-ID')}</option>`).join('');
  document.getElementById('pay_tagihan').innerHTML = opts || '<option value="">Tidak ada tagihan pending</option>';
}

function konfirmasiPembayaran(){
  const id = pay_tagihan.value;
  if(!id) return toast('Tidak ada tagihan untuk dibayar!','#dc2626');
  const data = DB.get('tagihan');
  const t = data.find(x=>x.id===id);
  t.status = 'paid';
  t.metode = pay_metode.value;
  t.pengirim = pay_nama.value || '-';
  t.waktu = new Date().toISOString();
  DB.set('tagihan', data);

  // Simpan transaksi
  const trx = DB.get('transaksi');
  trx.push({ id: t.id, nama: t.nama, bulan: t.bulan, nominal: t.nominal, metode: t.metode, waktu: t.waktu });
  DB.set('transaksi', trx);

  renderPembayaran(); fillSelectTagihan(); renderTagihan();
  toast('✅ Pembayaran berhasil dikonfirmasi!');
  pay_nama.value = '';
}

function renderPembayaran(){
  const trx = DB.get('transaksi').reverse();
  const tbl = document.getElementById('tblTransaksi');
  if(!trx.length){ tbl.innerHTML = '<tr><td colspan="6" style="text-align:center;color:#999;padding:20px">Belum ada transaksi</td></tr>'; return; }
  tbl.innerHTML = trx.map((t,i)=>`
    <tr>
      <td>${i+1}</td><td><b>${t.nama}</b></td><td>${t.bulan}</td>
      <td>Rp ${t.nominal.toLocaleString('id-ID')}</td><td>${t.metode}</td>
      <td>${new Date(t.waktu).toLocaleString('id-ID')}</td>
    </tr>`).join('');
}

/* ================= INCOME ================= */
let chartInstance = null;
function renderIncome(){
  const trx = DB.get('transaksi');
  const now = new Date();
  const bulanIni = now.toISOString().slice(0,7);
  const tahunIni = now.getFullYear().toString();

  const incBulan = trx.filter(t=>t.waktu.startsWith(bulanIni)).reduce((s,t)=>s+t.nominal,0);
  const incTahun = trx.filter(t=>t.waktu.startsWith(tahunIni)).reduce((s,t)=>s+t.nominal,0);

  document.getElementById('incBulanIni').textContent = 'Rp ' + incBulan.toLocaleString('id-ID');
  document.getElementById('incTahunIni').textContent = 'Rp ' + incTahun.toLocaleString('id-ID');
  document.getElementById('incTotal').textContent = trx.length;

  // Group per bulan
  const group = {};
  trx.forEach(t=>{
    const b = t.waktu.slice(0,7);
    if(!group[b]) group[b] = {count:0, total:0};
    group[b].count++; group[b].total += t.nominal;
  });
  const keys = Object.keys(group).sort().reverse();
  document.getElementById('tblIncome').innerHTML = keys.length ? keys.map(b=>`
    <tr><td><b>${b}</b></td><td>${group[b].count} transaksi</td>
    <td>Rp ${group[b].total.toLocaleString('id-ID')}</td></tr>`).join('')
    : '<tr><td colspan="3" style="text-align:center;color:#999;padding:20px">Belum ada data</td></tr>';
}

/* ================= GANGGUAN ================= */
function tambahGangguan(){
  const pid = g_pelanggan.value;
  if(!pid) return toast('Pilih pelanggan!','#dc2626');
  const pel = DB.get('pelanggan').find(p=>p.id===pid);
  const data = DB.get('gangguan');
  data.push({
    id: 'G' + Date.now(),
    pid, nama: pel.nama,
    jenis: g_jenis.value,
    desk: g_desk.value,
    tgl: g_tgl.value || new Date().toISOString().slice(0,10),
    status: 'pending'
  });
  DB.set('gangguan', data);
  g_desk.value = '';
  renderGangguan();
  toast('Laporan gangguan terkirim!','#dc2626');
}

function renderGangguan(){
  const data = DB.get('gangguan').reverse();
  const tbl = document.getElementById('tblGangguan');
  if(!data.length){ tbl.innerHTML = '<tr><td colspan="7" style="text-align:center;color:#999;padding:20px">Belum ada laporan</td></tr>'; return; }
  tbl.innerHTML = data.map((g,i)=>`
    <tr>
      <td>${i+1}</td><td><b>${g.nama}</b></td><td>${g.jenis}</td>
      <td>${g.desk||'-'}</td><td>${g.tgl}</td>
      <td>${g.status==='done'?'<span class="badge ok">Selesai</span>':'<span class="badge pending">Menunggu</span>'}</td>
      <td>
        ${g.status!=='done'?`<button class="btn green" style="padding:6px 10px;font-size:12px" onclick="selesaiGangguan('${g.id}')">Selesai</button>`:''}
        <button class="btn red" style="padding:6px 10px;font-size:12px" onclick="hapusGangguan('${g.id}')">Hapus</button>
      </td>
    </tr>`).join('');
}

function selesaiGangguan(id){
  const data = DB.get('gangguan');
 
