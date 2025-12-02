<!doctype html>
<html lang="tr">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>Sunum Bilgileri & Değerlendirme</title>
  <style>
    :root{--accent:#0b77d1;--muted:#666}
    body{font-family:Inter, system-ui, Arial; margin:0;background:#f6f8fb;color:#111}
    header{background:linear-gradient(90deg,#0b77d1, #3aa0ff);color:#fff;padding:18px 20px}
    .container{max-width:900px;margin:24px auto;padding:18px;background:#fff;border-radius:12px;box-shadow:0 6px 20px rgba(10,20,40,0.08)}
    h1{margin:0 0 6px;font-size:20px}
    .meta{color:var(--muted);font-size:13px}
    .row{display:flex;gap:16px;flex-wrap:wrap;margin-top:16px}
    .card{flex:1 1 280px;padding:14px;border-radius:10px;background:#fbfdff;border:1px solid #eef3fb}
    label{display:block;font-weight:600;margin-bottom:8px}
    input[type=text], input[type=url], textarea{width:100%;padding:10px;border-radius:8px;border:1px solid #dde7f2}
    textarea{min-height:100px}
    .center{text-align:center}
    .stars{display:inline-flex;gap:8px;align-items:center}
    .star{font-size:34px;cursor:pointer;user-select:none;opacity:0.36}
    .star.active{opacity:1;transform:scale(1.05)}
    .small{font-size:13px;color:var(--muted)}
    .img-preview{width:100%;height:180px;object-fit:cover;border-radius:8px;background:#edf6ff}
    .controls{display:flex;gap:8px;flex-wrap:wrap;justify-content:center;margin-top:12px}
    button{background:var(--accent);color:#fff;border:0;padding:8px 12px;border-radius:8px;cursor:pointer}
    .ghost{background:transparent;color:var(--accent);border:1px solid rgba(11,119,209,0.12)}
    footer{font-size:13px;color:var(--muted);text-align:center;margin-top:14px}
    .stats{display:flex;gap:12px;justify-content:center;align-items:center;margin-top:12px}
    .badge{background:#eef7ff;padding:8px 12px;border-radius:999px;border:1px solid #d7eefc}
    .edit-note{font-size:12px;color:#0b77d1;margin-top:8px}
    .admin{margin-top:10px;border-top:1px dashed #eef3fb;padding-top:12px}
  </style>
</head>
<body>
  <header>
    <div class="container" style="background:transparent;box-shadow:none;padding:0">
      <h1 id="presentationTitle">Sunum Başlığı (Düzenlemek için "Düzenle" tuşuna basın)</h1>
      <div class="meta" id="presentationSubtitle">Kısa özet veya sunumda anlatılanların özeti buraya gelecek.</div>
    </div>
  </header>

  <main class="container">
    <div class="row">
      <div class="card">
        <label for="nameInput">İsminiz</label>
        <input id="nameInput" type="text" placeholder="Adınızı girin...">
        <div style="margin-top:12px">
          <label>Sunumu 1-5 arası değerlendir</label>
          <div class="center">
            <div class="stars" id="stars">
              <span class="star" data-value="1">☆</span>
              <span class="star" data-value="2">☆</span>
              <span class="star" data-value="3">☆</span>
              <span class="star" data-value="4">☆</span>
              <span class="star" data-value="5">☆</span>
            </div>
            <div class="small" id="chosenRating">Henüz oy yok</div>
          </div>
          <div class="controls">
            <button id="submitRating">Oy Gönder</button>
            <button class="ghost" id="clearRatings">Tüm Oyları Temizle</button>
          </div>
        </div>
        <div class="stats">
          <div class="badge">Katılımcı: <span id="participantCount">0</span></div>
          <div class="badge">Ortalama Puan: <strong id="averageScore">—</strong></div>
        </div>
        <footer>Sunum başarısı = (toplam yıldızlar) / (katılımcı sayısı)</footer>
      </div>

      <div class="card">
        <label>Sunum İçeriği</label>
        <img id="presentationImage" src="" alt="Sunum görseli" class="img-preview">
        <div style="margin-top:8px">
          <div id="presentationSummary" style="white-space:pre-wrap">Sunumla ilgili kısa bilgiler, hap bilgiler, anahtar noktalar burada görünür. Bunu düzenlemek için "Düzenle" butonuna basın.</div>
        </div>

        <div class="edit-note">Bu alanı düzenleyip kaydettiğinizde herkes için güncellenecektir (localStorage).</div>

        <div class="admin">
          <label>Yönetici: İçeriği düzenle</label>
          <div style="display:flex;gap:8px;margin-top:8px;flex-direction:column">
            <input id="titleEdit" type="text" placeholder="Sayfa başlığı">
            <input id="subtitleEdit" type="text" placeholder="Kısa alt başlık">
            <textarea id="summaryEdit" placeholder="Sunum özeti..." rows="5"></textarea>
            <input id="imageUrlEdit" type="url" placeholder="Görsel URL'si (veya dosya yükleyin)">
            <input id="imageFile" type="file" accept="image/*">
            <div style="display:flex;gap:8px">
              <button id="saveContent">Kaydet</button>
              <button class="ghost" id="resetContent">Varsayılanları Yükle</button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div style="margin-top:18px;text-align:center;color:var(--muted)">
      <small>Not: Bu demo yerel olarak localStorage kullanır — veriler sadece tarayıcıda saklanır. Eğer herkesin sonuçlarını merkezi bir yerde toplamak isterseniz Google Sheets/Forms, Airtable veya gerçek bir sunucuya bağlanmak gerekir.</small>
    </div>
  </main>

  <script>
    // IDs used for quick editing guidance: presentationTitle, presentationSubtitle, presentationSummary, presentationImage
    const LS_KEY = 'presentation_qr_demo_v1';

    // DOM
    const titleEl = document.getElementById('presentationTitle');
    const subtitleEl = document.getElementById('presentationSubtitle');
    const summaryEl = document.getElementById('presentationSummary');
    const imageEl = document.getElementById('presentationImage');

    const nameInput = document.getElementById('nameInput');
    const starsWrap = document.getElementById('stars');
    const chosenRating = document.getElementById('chosenRating');
    const submitBtn = document.getElementById('submitRating');
    const participantCount = document.getElementById('participantCount');
    const averageScore = document.getElementById('averageScore');
    const clearBtn = document.getElementById('clearRatings');

    // Admin inputs
    const titleEdit = document.getElementById('titleEdit');
    const subtitleEdit = document.getElementById('subtitleEdit');
    const summaryEdit = document.getElementById('summaryEdit');
    const imageUrlEdit = document.getElementById('imageUrlEdit');
    const imageFile = document.getElementById('imageFile');
    const saveContent = document.getElementById('saveContent');
    const resetContent = document.getElementById('resetContent');

    // Default content
    const DEFAULT = {
      title: 'Sunum Başlığı',
      subtitle: 'Sunum hakkında kısa bir özet...',
      summary: 'Hap bilgiler:\n- 1. Nokta\n- 2. Nokta\n- 3. Sonuç',
      image: '',
      ratings: [] // {name,score,timestamp}
    };

    // load
    function loadState(){
      try{
        const raw = localStorage.getItem(LS_KEY);
        if(!raw) return {...DEFAULT};
        return JSON.parse(raw);
      }catch(e){console.error(e); return {...DEFAULT};}
    }

    function saveState(state){
      localStorage.setItem(LS_KEY, JSON.stringify(state));
    }

    let state = loadState();

    function renderContent(){
      titleEl.textContent = state.title || DEFAULT.title;
      subtitleEl.textContent = state.subtitle || DEFAULT.subtitle;
      summaryEl.textContent = state.summary || DEFAULT.summary;
      if(state.image) imageEl.src = state.image; else imageEl.src = '';

      // fill admin
      titleEdit.value = state.title || '';
      subtitleEdit.value = state.subtitle || '';
      summaryEdit.value = state.summary || '';
      imageUrlEdit.value = state.image || '';

      renderStats();
    }

    function renderStats(){
      const arr = state.ratings || [];
      const count = arr.length;
      participantCount.textContent = count;
      if(count===0) averageScore.textContent = '—';
      else{
        const sum = arr.reduce((s,r)=>s + Number(r.score),0);
        const avg = Math.round((sum/count) * 100) / 100; // iki ondalık
        averageScore.textContent = avg;
      }
    }

    renderContent();

    // stars interaction
    let currentHover = 0;
    let chosen = 0;
    function updateStarsUI(val){
      Array.from(starsWrap.children).forEach(el=>{
        const v = Number(el.dataset.value);
        el.textContent = v <= val ? '★' : '☆';
        el.classList.toggle('active', v <= val);
      });
      chosenRating.textContent = val ? `${val} yıldız seçildi` : 'Henüz oy yok';
    }

    starsWrap.addEventListener('click', e=>{
      if(e.target.classList.contains('star')){
        chosen = Number(e.target.dataset.value);
        updateStarsUI(chosen);
      }
    });

    starsWrap.addEventListener('mouseover', e=>{
      if(e.target.classList.contains('star')){
        const v = Number(e.target.dataset.value);
        updateStarsUI(v);
      }
    });
    starsWrap.addEventListener('mouseleave', ()=>{
      updateStarsUI(chosen);
    });

    submitBtn.addEventListener('click', ()=>{
      const name = nameInput.value.trim() || 'Anonim';
      if(chosen < 1 || chosen > 5){alert('Lütfen 1-5 arası bir yıldız seçin.'); return;}
      state.ratings.push({name,score:chosen,timestamp:Date.now()});
      saveState(state);
      renderStats();
      alert('Oyunuz kaydedildi, teşekkürler!');
      // reset UI
      nameInput.value = '';
      chosen = 0; updateStarsUI(0);
    });

    clearBtn.addEventListener('click', ()=>{
      if(!confirm('Tüm oyları silmek istediğinize emin misiniz?')) return;
      state.ratings = [];
      saveState(state);
      renderStats();
      alert('Tüm oylar silindi.');
    });

    // Admin save
    saveContent.addEventListener('click', ()=>{
      state.title = titleEdit.value || DEFAULT.title;
      state.subtitle = subtitleEdit.value || DEFAULT.subtitle;
      state.summary = summaryEdit.value || DEFAULT.summary;
      const imgUrl = imageUrlEdit.value.trim();
      if(imgUrl) state.image = imgUrl;
      saveState(state);
      renderContent();
      alert('İçerik kaydedildi.');
    });

    resetContent.addEventListener('click', ()=>{
      if(!confirm('Varsayılan içeriğe dönmek istediğinizden emin misiniz?')) return;
      state = {...DEFAULT};
      saveState(state);
      renderContent();
      alert('Varsayılan yüklendi.');
    });

    // Support uploading image file -> base64 stored
    imageFile.addEventListener('change', ()=>{
      const f = imageFile.files[0];
      if(!f) return;
      const reader = new FileReader();
      reader.onload = ()=>{
        state.image = reader.result;
        imageUrlEdit.value = '';
        saveState(state);
        renderContent();
        alert('Görsel yüklendi ve kaydedildi.');
      };
      reader.readAsDataURL(f);
    });

    // small helper: allow manually clearing localStorage key from console
    window.__presentation_debug = {stateKey:LS_KEY, clear: ()=>{localStorage.removeItem(LS_KEY);location.reload();}};
  </script>
</body>
</html>
