
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>WiFi Bill Management - Ahmad Basyit</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
  *{margin:0;padding:0;box-sizing:border-box;font-family:Arial,sans-serif}
  body{background:#f0f2f5;color:#333}
  header{background:#1e3c72;color:#fff;padding:16px 24px}
  header h1{font-size:20px}
  header small{opacity:.8;font-size:12px}
  nav{background:#fff;display:flex;gap:2px;padding:0 16px;box-shadow:0 2px 6px rgba(0,0,0,.08);overflow-x:auto}
  nav button{background:none;border:none;padding:14px 18px;cursor:pointer;font-size:14px;font-weight:bold;color:#666;border-bottom:3px solid transparent;white-space:nowrap}
  nav button.active{color:#2a5298;border-bottom-color:#2a5298}
  main{padding:20px;max-width:1100px;margin:auto}
  .page{display:none}.page.active{display:block}
  .card{background:#fff;border-radius:10px;padding:20px;box-shadow:0 2px 10px rgba(0,0,0,.06);margin-bottom:18px}
  .card h2{font-size:17px;margin-bottom:14px;color:#1e3c72;border-left:4px solid #2a5298;padding-left:10px}
  .grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:12px}
  label{font-size:12px;font-weight:bold;color:#555;display:block;margin-bottom:4px}
  input,select,textarea{width:100%;padding:9px;border:1px solid #ccc;border-radius:6px;font-size:13px}
  input:focus,select:focus,textarea:focus{outline:none;border-color:#2a5298}
  .btn{background:#2a5298;color:#fff;border:none;padding:10px 18px;border-radius:6px;font-size:13px;font-weight:bold;cursor:pointer}
  .btn:hover{background:#1e3c72}
  .btn.green{background:#16a34a}.btn.green:hover{background:#15803d}
  .btn.red{background:#dc2626}.btn.red:hover{background:#b91c1c}
  .btn.gray{background:#6b7280}.btn.gray:hover{background:#4b5563}
  table{width:100%;border-collapse:collapse;font-size:12px}
  th,td{text-align:left;padding:8px 6px;border-bottom:1px solid #eee}
  th{background:#f8fafc;color:#475569;font-size:11px;text-transform:uppercase}
  .badge{display:inline-block;padding:2px 8px;border-radius:12px;font-size:10px;font-weight:bold}
  .badge.ok{background:#dcfce7;color:#166534}
  .badge.pending{background:#fef3c7;color:#92400e}
  .stat-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(180px,1fr));gap:14px;margin-bottom:18px}
  .stat{background:#fff;padding:16px;border-radius:10px;box-shadow:0 2px 8px rgba(0,0,0,.05);border-left:4px solid #2a5298}
  .stat small{color:#64748b;font-size:11px;font-weight:bold}
  .stat h3{font-size:22px;margin-top:4px;color:#1e3c72}
  .toast{position:fixed;bottom:20px;right:20px;background:#16a34a;color:#fff;padding:12px 20px;border-radius:8px;font-weight:bold;opacity:0;transition:.3s;z-index:999;font-size:13px}
  .toast.show{opacity:1}
  .flex{display:flex;gap:8px;flex-wrap:wrap;align-items:center}
</style>
</head>
<body>

<header>
  <h1>🌐 WiFi Bill Management</h1>
  <small>Ahmad Basyit — Sistem Penagihan Online</small>
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
    <div class="stat" style="border-left-color:#f59e0b"><small>Tagihan Pending</small><h3 id="statPending">0</h3></div>
    <div class="stat" style="border-left-color:#dc2626"><small>Laporan Gangguan</small><h3 id="statGangguan">0</h3></div>
  </div>
  <div class="card">
    <h2>Grafik Income Bulanan</h2>
    <canvas id="chartIncome" height="80"></canvas>
  </div>
</section>

<!-- PELANGGAN -->
<section id="pelanggan" class="page">
  <div class="card">
    <h2>Tambah Pelanggan</h2>
    <div class="grid">
      <div><label>Nama</label><input id="p_nama"></div>
      <div><label>NIK</label><input id="p_nik"></div>
      <div><label>Alamat</label><input id="p_alamat"></div>
      <div><label>Telepon</label><input id="p_telp"></div>
      <div><label>Email</label><input id="p_email"></div>
      <div><label>Tgl Pemasangan</label><input id="p_tgl" type="date"></div>
      <div><label>Dokumentasi (Foto)</label><input id="p_dok" type="file" accept="image/*"></div>
    </div>
    <div style="margin-top:14px"><button class="btn" onclick="tambahPelanggan()">➕ Simpan</button></div>
  </div>
  <div class="card">
    <h2>Daftar Pelanggan</h2>
    <div style="overflow-x:auto">
      <table>
        <thead><tr><th>#</th><th>Nama</th><th>NIK</th><th>Alamat</th><th>Telp</th><th>Email</th><th>Tgl</th><th>Foto</th><th>Aksi</th></tr></thead>
        <tbody id="tblPelanggan"></tbody>
      </table>
    </div>
  </div>
</section>

<!-- TAGIHAN -->
<section id="tagihan" class="page">
  <div class="card">
    <h2>Generate Tagihan</h2>
    <div class="grid">
      <div><label>Pelanggan</label><select id="t_pelanggan"></select></div>
      <div><label>Periode (Bulan)</label><input id="t_bulan" type="month"></div>
      <div><label>Nominal (Rp)</label><input id="t_nominal" type="number" value="150000"></div>
    </div>
    <div style="margin-top:14px"><button class="btn" onclick="generateTagihan()">🧾 Buat Tagihan</button></div>
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
    <h2>Rekening Transfer</h2>
    <div class="grid">
      <div style="background:#f8fafc;padding:14px;border-radius:8px;border-left:4px solid #1e3c72">
        <b>🏦 Bank Mandiri</b><br>No. Rek: <b>1640006614236</b><br>A/N: <b>AHMAD BASYIT</b>
      </div>
      <div style="background:#f8fafc;padding:14px;border-radius:8px;border-left:4px solid #dc2626">
        <b>🏦 Bank BCA</b><br>No. Rek: <b>4730892069</b><br>A/N: <b>AHMAD BASYIT</b>
      </div>
    </div>
  </div>
  <div class="card">
    <h2>Konfirmasi Pembayaran</h2>
    <div class="grid">
      <div><label>Pilih Tagihan</label><select id="pay_tagihan"></select></div>
      <div><label>Metode</label>
        <select id="pay_metode"><option>Bank Mandiri</option><option>Bank BCA</option></select>
      </div>
      <div><label>Nama Pengirim</label><input id="pay_nama"></div>
    </div>
    <div style="margin-top:14px"><button class="btn green" onclick="konfirmasiBayar()">✅ Konfirmasi Sudah Bayar</button></div>
  </div>
  <div class="card">
    <h2>Riwayat Transaksi</h2>
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
    <div class="stat"><small>Income Bulan Ini</small><h3 id="incBulan">Rp 0</h3></div>
    <div class="stat" style="border-left-color:#16a34a"><small>Income Tahun Ini</small><h3 id="incTahun">Rp 0</h3></div>
    <div class="stat" style="border-left-color:#f59e0b"><small>Total Transaksi</small><h3 id="incTotal">0</h3></div>
  </div>
  <div class="card">
    <h2>Income per Bulan</h2>
    <div style="overflow-x:auto">
      <table>
        <thead><tr><th>Periode</th><th>Jumlah Trx</th><th>Total Income</th></tr></thead>
        <tbody id="tblIncome"></tbody>
      </table>
    </div>
  </div>
</section>

<!-- GANGGUAN -->
<section id="gangguan" class="page">
  <div class="card">
    <h2>Lapor Gangguan</h2>
    <div class="grid">
      <div><label>Pelanggan</label><select id="g_pelanggan"></select></div>
      <div><label>Jenis</label>
        <select id="g_jenis">
          <option>Internet Lambat</option><option>Koneksi Putus</option>
          <option>Router Mati</option><option>Lainnya</option>
        </select>
      </div>
      <div><label>Tanggal</label><input id="g_tgl" type="date"></div>
      <div style="grid-column:1/-1"><label>Deskripsi</label><textarea id="g_desk" rows="2"></textarea></div>
    </div>
    <div style="margin-top:14px"><button class="btn red" onclick="tambahGangguan()">⚠️ Kirim Laporan</button></div>
  </div>
  <div class="card">
    <h2>Daftar Gangguan</h2>
    <div style="overflow-x:auto">
      <table>
        <thead><tr><th>#</th><th>Pelanggan</th><th>Jenis</th><th>Deskripsi</th><th>Tgl</th><th>Status</th><th>Aksi</th></tr></thead>
        <tbody id="tblGangguan"></tbody>
      </table>
    </div>
  </div>
</section>

</main>

<div class="toast" id="toast"></div>

<script>
/* ============ STORAGE ============ */
function get(k){ return JSON.parse(localStorage.getItem('wifi_'+k) || '[]'); }
function set(k,v){ localStorage.setItem('wifi_'+k, JSON.stringify(v)); }

/* ============ NAV ============ */
function showPage(id, btn){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  if(btn) btn.classList.add('active');
  if(id==='dashboard') renderDashboard();
  if(id==='pelanggan') renderPelanggan();
  if(id==='tagihan'){ renderTagihan(); fillSelect('t_pelanggan'); }
  if(id==='pembayaran'){ renderTransaksi(); fillPayTagihan(); }
  if(id==='income') renderIncome();
  if(id==='gangguan'){ renderGangguan(); fillSelect('g_pelanggan'); }
}

/* ============ TOAST ============ */
function toast(msg, color){
  color = color || '#16a34a';
  var t = document.getElementById('toast');
  t.textContent = msg; t.style.background = color;
  t.classList.add('show');
  setTimeout(function(){ t.classList.remove('show'); }, 2500);
}

/* ============ PELANGGAN ============ */
function tambahPelanggan(){
  var nama = document.getElementById('p_nama').value.trim();
  var nik = document.getElementById('p_nik').value.trim();
  if(!nama || !nik) return toast('Nama & NIK wajib diisi', '#dc2626');

  var file = document.getElementById('p_dok').files[0];
  var proses = function(dokData){
    var data = get('pelanggan');
    data.push({
      id: 'P' + Date.now(),
      nama: nama, nik: nik,
      alamat: document.getElementById('p_alamat').value,
      telp: document.getElementById('p_telp').value,
      email: document.getElementById('p_email').value,
      tgl: document.getElementById('p_tgl').value,
      dok: dokData
    });
    set('pelanggan', data);
    ['p_nama','p_nik','p_alamat','p_telp','p_email','p_tgl','p_dok'].forEach(function(id){
      document.getElementById(id).value = '';
    });
    renderPelanggan();
    toast('Pelanggan ditambahkan');
  };

  if(file){
    var reader = new FileReader();
    reader.onload = function(e){ proses(e.target.result); };
    reader.readAsDataURL(file);
  } else {
    proses('');
  }
}

function renderPelanggan(){
  var data = get('pelanggan');
  var tbl = document.getElementById('tblPelanggan');
  if(!data.length){
    tbl.innerHTML = '<tr><td colspan="9" style="text-align:center;color:#999;padding:16px">Belum ada pelanggan</td></tr>';
    return;
  }
  var html = '';
  data.forEach(function(p,i){
    var foto = p.dok ? '<img src="'+p.dok+'" style="width:36px;height:36px;object-fit:cover;border-radius:4px">' : '-';
    html += '<tr><td>'+(i+1)+'</td><td><b>'+p.nama+'</b></td><td>'+p.nik+'</td><td>'+(p.alamat||'-')+'</td><td>'+(p.telp||'-')+'</td><td>'+(p.email||'-')+'</td><td>'+(p.tgl||'-')+'</td><td>'+foto+'</td>';
    html += '<td><button class="btn red" style="padding:4px 8px;font-size:11px" onclick="hapusPelanggan(\''+p.id+'\')">Hapus</button></td></tr>';
  });
  tbl.innerHTML = html;
}

function hapusPelanggan(id){
  if(!confirm('Hapus pelanggan ini?')) return;
  set('pelanggan', get('pelanggan').filter(function(p){ return p.id !== id; }));
  renderPelanggan();
  toast('Pelanggan dihapus', '#dc2626');
}

/* ============ FILL SELECT ============ */
function fillSelect(selectId){
  var data = get('pelanggan');
  var el = document.getElementById(selectId);
  var html = '';
  data.forEach(function(p){ html += '<option value="'+p.id+'">'+p.nama+'</option>'; });
  el.innerHTML = html || '<option value="">(Belum ada pelanggan)</option>';
}

function fillPayTagihan(){
  var data = get('tagihan').filter(function(t){ return t.status !== 'paid'; });
  var el = document.getElementById('pay_tagihan');
  var html = '';
  data.forEach(function(t){
    html += '<option value="'+t.id+'">'+t.nama+' — '+t.bulan+' — Rp '+t.nominal.toLocaleString('id-ID')+'</option>';
  });
  el.innerHTML = html || '<option value="">Tidak ada tagihan pending</option>';
}

/* ============ TAGIHAN ============ */
function generateTagihan(){
  var pid = document.getElementById('t_pelanggan').value;
  var bulan = document.getElementById('t_bulan').value;
  var nominal = parseInt(document.getElementById('t_nominal').value);
  if(!pid || !bulan || !nominal) return toast('Lengkapi semua data', '#dc2626');

  var pel = get('pelanggan').find(function(p){ return p.id === pid; });
  if(!pel) return toast('Pelanggan tidak ditemukan', '#dc2626');

  var data = get('tagihan');
  var duplikat = data.find(function(t){ return t.pid === pid && t.bulan === bulan; });
  if(duplikat) return toast('Tagihan periode ini sudah ada', '#f59e0b');

  data.push({
    id: 'T' + Date.now(),
    pid: pid, nama: pel.nama, bulan: bulan, nominal: nominal,
    status: 'pending', tglBuat: new Date().toISOString()
  });
  set('tagihan', data);
  renderTagihan();
  toast('Tagihan dibuat');
}

function renderTagihan(){
  var data = get('tagihan').slice().reverse();
  var tbl = document.getElementById('tblTagihan');
  if(!data.length){
    tbl.innerHTML = '<tr><td colspan="6" style="text-align:center;color:#999;padding:16px">Belum ada tagihan</td></tr>';
    return;
  }
  var html = '';
  data.forEach(function(t,i){
    var status = t.status === 'paid'
      ? '<span class="badge ok">Lunas</span>'
      : '<span class="badge pending">Belum Bayar</span>';
    html += '<tr><td>'+(i+1)+'</td><td><b>'+t.nama+'</b></td><td>'+t.bulan+'</td>';
    html += '<td>Rp '+t.nominal.toLocaleString('id-ID')+'</td><td>'+status+'</td>';
    html += '<td><button class="btn" style="padding:4px 8px;font-size:11px" onclick="cetakTagihan(\''+t.id+'\')">🖨️ Cetak</button> ';
    if(t.status !== 'paid'){
      html += '<button class="btn red" style="padding:4px 8px;font-size:11px" onclick="hapusTagihan(\''+t.id+'\')">Hapus</button>';
    }
    html += '</td></tr>';
  });
  tbl.innerHTML = html;
}

function hapusTagihan(id){
  if(!confirm('Hapus tagihan?')) return;
  set('tagihan', get('tagihan').filter(function(t){ return t.id !== id; }));
  renderTagihan();
  toast('Tagihan dihapus', '#dc2626');
}

/* ============ CETAK TAGIHAN (via print browser) ============ */
function cetakTagihan(id){
  var t = get('tagihan').find(function(x){ return x.id === id; });
  if(!t) return;
  var pel = get('pelanggan').find(function(p){ return p.id === t.pid; }) || {};
  var win = window.open('', '_blank', 'width=700,height=800');
  win.document.write(
    '<html><head><title>Tagihan '+t.nama+'</title>' +
    '<style>body{font-family:Arial;padding:30px}h2{background:#1e3c72;color:#fff;padding:12px;margin:0 0 20px}' +
    'table{width:100%;border-collapse:collapse;font-size:13px}td{padding:6px 4px;border-bottom:1px solid #eee}' +
    '.total{background:#f0f2f5;font-weight:bold;font-size:15px}.info{margin-top:20px;padding:12px;background:#f8fafc;border-radius:6px;font-size:12px}' +
    'small{color:#888}</style></head><body>' +
    '<h2>TAGIHAN INTERNET WIFI</h2>' +
    '<small>Ahmad Basyit — WiFi Bill Management</small>' +
    '<table style="margin-top:16px">' +
    '<tr><td width="130"><b>No. Tagihan</b></td><td>'+t.id+'</td></tr>' +
    '<tr><td><b>Nama</b></td><td>'+t.nama+'</td></tr>' +
    '<tr><td><b>NIK</b></td><td>'+(pel.nik||'-')+'</td></tr>' +
    '<tr><td><b>Alamat</b></td><td>'+(pel.alamat||'-')+'</td></tr>' +
    '<tr><td><b>Telepon</b></td><td>'+(pel.telp||'-')+'</td></tr>' +
    '<tr><td><b>Periode</b></td><td>'+t.bulan+'</td></tr>' +
    '<tr><td><b>Status</b></td><td>'+(t.status==='paid'?'LUNAS':'BELUM BAYAR')+'</td></tr>' +
    '<tr class="total"><td>TOTAL</td><td>Rp '+t.nominal.toLocaleString('id-ID')+'</td></tr>' +
    '</table>' +
    '<div class="info"><b>Transfer ke:</b><br>' +
    'Bank Mandiri: 1640006614236 (A/N AHMAD BASYIT)<br>' +
    'Bank BCA: 4730892069 (A/N AHMAD BASYIT)</div>' +
    '<p style="font-size:11px;color:#888;margin-top:20px">Harap konfirmasi pembayaran setelah transfer. Terima kasih.</p>' +
    '<script>window.onload=function(){window.print()}<\/script>' +
    '</body></html>'
  );
  win.document.close();
}

/* ============ PEMBAYARAN ============ */
function konfirmasiBayar(){
  var id = document.getElementById('pay_tagihan').value;
  if(!id) return toast('Tidak ada tagihan', '#dc2626');

  var data = get('tagihan');
  var t = data.find(function(x){ return x.id === id; });
  if(!t) return;

  t.status = 'paid';
  t.metode = document.getElementById('pay_metode').value;
  t.pengirim = document.getElementById('pay_nama').value || '-';
  t.waktu = new Date().toISOString();
  set('tagihan', data);

  var trx = get('transaksi');
  trx.push({
    id: t.id, nama: t.nama, bulan: t.bulan, nominal: t.nominal,
    metode: t.metode, waktu: t.waktu
  });
  set('transaksi', trx);

  document.getElementById('pay_nama').value = '';
  renderTransaksi();
  fillPayTagihan();
  renderTagihan();
  toast('✅ Pembayaran berhasil dikonfirmasi');
}

function renderTransaksi(){
  var data = get('transaksi').slice().reverse();
  var tbl = document.getElementById('tblTransaksi');
  if(!data.length){
    tbl.innerHTML = '<tr><td colspan="6" style="text-align:center;color:#999;padding:16px">Belum ada transaksi</td></tr>';
    return;
  }
  var html = '';
  data.forEach(function(t,i){
    html += '<tr><td>'+(i+1)+'</td><td><b>'+t.nama+'</b></td><td>'+t.bulan+'</td>';
    html += '<td>Rp '+t.nominal.toLocaleString('id-ID')+'</td><td>'+t.metode+'</td>';
    html += '<td>'+new Date(t.waktu).toLocaleString('id-ID')+'</td></tr>';
  });
  tbl.innerHTML = html;
}

/* ============ INCOME ============ */
var chartInstance = null;
function renderIncome(){
  var trx = get('transaksi');
  var now = new Date();
  var bulanIni = now.toISOString().slice(0,7);
  var tahunIni = String(now.getFullYear());

  var incBulan = 0, incTahun = 0;
  trx.forEach(function(t){
    if(t.waktu.slice(0,7) === bulanIni) incBulan += t.nominal;
    if(t.waktu.slice(0,4) === tahunIni) incTahun += t.nominal;
  });

  document.getElementById('incBulan').textContent = 'Rp ' + incBulan.toLocaleString('id-ID');
  document.getElementById('incTahun').textContent = 'Rp ' + incTahun.toLocaleString('id-ID');
  document.getElementById('incTotal').textContent = trx.length;

  var group = {};
  trx.forEach(function(t){
    var b = t.waktu.slice(0,7);
    if(!group[b]) group[b] = { count: 0, total: 0 };
    group[b].count++;
    group[b].total += t.nominal;
  });
  var keys = Object.keys(group).sort().reverse();
  var tbl = document.getElementById('tblIncome');
  if(!keys.length){
    tbl.innerHTML = '<tr><td colspan="3" style="text-align:center;color:#999;padding:16px">Belum ada data</td></tr>';
    return;
  }
  var html = '';
  keys.forEach(function(b){
    html += '<tr><td><b>'+b+'</b></td><td>'+group[b].count+' trx</td>';
    html += '<td>Rp '+group[b].total.toLocaleString('id-ID')+'</td></tr>';
  });
  tbl.innerHTML = html;
}

/* ============ DASHBOARD ============ */
function renderDashboard(){
  var pel = get('pelanggan');
  var tag = get('tagihan');
  var gang = get('gangguan');
  var trx = get('transaksi');

  document.getElementById('statPelanggan').textContent = pel.length;
  document.getElementById('statPending').textContent = tag.filter(function(t){ return t.status !== 'paid'; }).length;
  document.getElementById('statGangguan').textContent = gang.length;

  var now = new Date();
  var bulanIni = now.toISOString().slice(0,7);
  var incBulan = trx.filter(function(t){ return t.waktu.slice(0,7) === bulanIni; })
                    .reduce(function(s,t){ return s + t.nominal; }, 0);
  document.getElementById('statIncome').textContent = 'Rp ' + incBulan.toLocaleString('id-ID');

  // Chart
  var group = {};
  trx.forEach(function(t){
    var b = t.waktu.slice(0,7);
    group[b] = (group[b] || 0) + t.nominal;
  });
  var labels = Object.keys(group).sort();
  var values = labels.map(function(b){ return group[b]; });

  var ctx = document.getElementById('chartIncome').getContext('2d');
  if(chartInstance) chartInstance.destroy();
  chartInstance = new Chart(ctx, {
    type: 'bar',
    data: {
      labels: labels.length ? labels : ['Belum ada data'],
      datasets: [{
        label: 'Income (Rp)',
        data: values.length ? values : [0],
        backgroundColor: '#2a5298'
      }]
    },
    options: {
      responsive: true,
      plugins: { legend: { display: false } },
      scales: { y: { beginAtZero: true } }
    }
  });
}

/* ============ GANGGUAN ============ */
function tambahGangguan(){
  var pid = document.getElementById('g_pelanggan').value;
  if(!pid) return toast('Pilih pelanggan', '#dc2626');
  var pel = get('pelanggan').find(function(p){ return p.id === pid; });
  if(!pel) return;

  var data = get('gangguan');
  data.push({
    id: 'G' + Date.now(),
    pid: pid, nama: pel.nama,
    jenis: document.getElementById('g_jenis').value,
    desk: document.getElementById('g_desk').value,
    tgl: document.getElementById('g_tgl').value || new Date().toISOString().slice(0,10),
    status: 'pending'
  });
  set('gangguan', data);
  document.getElementById('g_desk').value = '';
  renderGangguan();
  toast('Laporan terkirim', '#dc2626');
}

function renderGangguan(){
  var data = get('gangguan').slice().reverse();
  var tbl = document.getElementById('tblGangguan');
  if(!data.length){
    tbl.innerHTML = '<tr><td colspan="7" style="text-align:center;color:#999;padding:16px">Belum ada laporan</td></tr>';
    return;
  }
  var html = '';
  data.forEach(function(g,i){
    var status = g.status === 'done'
      ? '<span class="badge ok">Selesai</span>'
      : '<span class="badge pending">Menunggu</span>';
    html += '<tr><td>'+(i+1)+'</td><td><b>'+g.nama+'</b></td><td>'+g.jenis+'</td>';
    html += '<td>'+(g.desk||'-')+'</td><td>'+g.tgl+'</td><td>'+status+'</td><td>';
    if(g.status !== 'done'){
      html += '<button class="btn green" style="padding:4px 8px;font-size:11px" onclick="selesaiGangguan(\''+g.id+'\')">Selesai</button> ';
    }
    html += '<button class="btn red" style="padding:4px 8px;font-size:11px" onclick="hapusGangguan(\''+g.id+'\')">Hapus</button>';
    html += '</td></tr>';
  });
  tbl.innerHTML = html;
}

function selesaiGangguan(id){
  var data = get('gangguan');
  var g = data.find(function(x){ return x.id === id; });
  if(g) g.status = 'done';
  set('gangguan', data);
  renderGangguan();
  toast('Gangguan ditandai selesai');
}

function hapusGangguan(id){
  if(!confirm('Hapus laporan?')) return;
  set('gangguan', get('gangguan').filter(function(g){ return g.id !== id; }));
  renderGangguan();
  toast('Laporan dihapus', '#dc2626');
}

/* ============ INIT ============ */
renderDashboard();
</script>
</body>
</html>
