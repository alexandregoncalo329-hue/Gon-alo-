<!--
ProcuraJá - Single-file demo site (HTML/CSS/JS)
Features:
- Professional blue/white design
- Multilingual (PT/EN/FR/ZH) with language switcher
- Categories: Casas, Carros, Empregos, Serviços
- Publish form (free or paid). If paid, shows IBAN/Multicaixa details and allows user to upload receipt.
- Admin panel (password-protected client-side) to review/approve ads and payments (stored in localStorage for demo)

How to use:
1. Save this file as procuraja.html and open in a browser.
2. To publish: fill form, if paid upload receipt, click Publish. Ads are saved to localStorage.
3. Admin: click "Admin" in header and enter password: admin123 (change after). There you can Approve/Reject ads.

Note: This is a client-side demo without a backend. For production you'll need a server, database and secure payment integration.
--><!doctype html>

<html lang="pt">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>ProcuraJá — Anúncios</title>
  <style>
    :root{--blue:#0b67c2;--dark:#073a6a;--muted:#6b7280}
    body{font-family:Inter,system-ui,Arial,sans-serif;margin:0;background:#f6fbff;color:#04253d}
    header{background:linear-gradient(90deg,var(--blue),#1e88e5);color:white;padding:18px 20px;display:flex;align-items:center;justify-content:space-between}
    .brand{font-weight:700;font-size:20px}
    nav{display:flex;gap:12px;align-items:center}
    .lang{background:rgba(255,255,255,0.12);border:none;color:white;padding:8px;border-radius:6px}
    main{max-width:1100px;margin:24px auto;padding:0 16px}
    .grid{display:grid;grid-template-columns:1fr 360px;gap:20px}
    .card{background:white;border-radius:10px;padding:16px;box-shadow:0 6px 18px rgba(11,103,194,0.06)}
    .search{display:flex;gap:8px}.cat{display:flex;gap:8px;flex-wrap:wrap;margin-top:12px}
.cat button{background:transparent;border:1px solid #e6eefc;padding:8px 10px;border-radius:8px;color:var(--dark)}

.ad{border-bottom:1px solid #eef6ff;padding:12px 0}
.ad h4{margin:0 0 6px}
.ad p{margin:0;color:var(--muted)}

.btn{background:var(--blue);color:white;border:none;padding:10px 12px;border-radius:8px;cursor:pointer}
.btn-ghost{background:transparent;border:1px solid #e6eefc;color:var(--dark)}

footer{max-width:1100px;margin:20px auto;color:var(--muted);padding:8px 16px}

form label{display:block;margin-top:10px;font-size:14px}
input,select,textarea{width:100%;padding:8px;border-radius:6px;border:1px solid #e6eefc;margin-top:6px}
.small{font-size:13px;color:var(--muted)}

.payment-box{background:linear-gradient(180deg,#eef8ff,#ffffff);padding:10px;border-radius:8px;border:1px solid #d7efff}
.admin-link{background:rgba(255,255,255,0.12);border:1px solid rgba(255,255,255,0.18);color:white;padding:8px;border-radius:6px}

/* responsive */
@media(max-width:920px){.grid{grid-template-columns:1fr}.card{padding:12px}}

  </style>
</head>
<body>
  <header>
    <div class="brand">ProcuraJá</div>
    <nav>
      <button class="admin-link" id="openAdmin">Admin</button>
      <select id="lang" class="lang" aria-label="Idioma">
        <option value="pt">PT</option>
        <option value="en">EN</option>
        <option value="fr">FR</option>
        <option value="zh">中文</option>
      </select>
    </nav>
  </header>  <main>
    <div class="grid">
      <section>
        <div class="card">
          <div style="display:flex;justify-content:space-between;align-items:center">
            <div>
              <h2 id="heroTitle">Anúncios recentes</h2>
              <p id="heroSubtitle" class="small">Encontre casas, carros, empregos e serviços em Angola — destaque para Luanda.</p>
            </div>
            <div>
              <input id="searchInput" placeholder="Pesquisar..." />
              <button class="btn" id="searchBtn">Pesquisar</button>
            </div>
          </div><div class="cat" style="margin-top:18px">
        <button data-cat="Todos">Todos</button>
        <button data-cat="Casas">Casas</button>
        <button data-cat="Carros">Carros</button>
        <button data-cat="Empregos">Empregos</button>
        <button data-cat="Serviços">Serviços</button>
      </div>

      <div id="adsList" style="margin-top:14px"></div>
    </div>

    <div class="card" style="margin-top:16px">
      <h3 id="howItWorksTitle">Como funciona</h3>
      <ol id="howItWorksList">
        <li>Publica o teu anúncio (grátis ou pago).</li>
        <li>Se escolheres pago, faz a transferência e envia o comprovativo.</li>
        <li>Após validação, o anúncio é ativado.</li>
      </ol>
    </div>
  </section>

  <aside>
    <div class="card">
      <h3 id="publishTitle">Publicar anúncio</h3>
      <form id="postForm">
        <label id="labelTitle">Título</label>
        <input name="title" required />
        <label id="labelCategory">Categoria</label>
        <select name="category">
          <option>Casas</option>
          <option>Carros</option>
          <option>Empregos</option>
          <option>Serviços</option>
        </select>
        <label id="labelDesc">Descrição</label>
        <textarea name="desc" rows="4"></textarea>
        <label id="labelCity">Cidade</label>
        <input name="city" placeholder="Luanda" />

        <label>Tipo</label>
        <select name="type" id="adType">
          <option value="free">Grátis</option>
          <option value="paid">Pago (destaque)</option>
        </select>

        <div id="paymentSection" style="display:none;" class="payment-box">
          <p><strong>Pagamento por IBAN / Multicaixa Express</strong></p>
          <p class="small">IBAN: <code>0051 0000 15342958101 66</code></p>
          <p class="small">Titular: <em>Alexandre Martins Gonçalo Da Costa Ventura</em></p>
          <p class="small">Multicaixa Express: <code>921122984</code></p>
          <label>Valor (Kz)</label>
          <input name="amount" placeholder="Ex: 1500" />
          <label>Carregar comprovativo</label>
          <input type="file" name="receipt" accept="image/*,application/pdf" />
        </div>

        <label>Contactos (telefone ou email)</label>
        <input name="contact" required />

        <button class="btn" type="submit">Publicar anúncio</button>
      </form>
    </div>

    <div class="card" style="margin-top:12px">
      <h4 id="helpTitle">Suporte</h4>
      <p class="small">Para dúvidas, contacta via WhatsApp: <strong>+244 921 122 984</strong></p>
    </div>
  </aside>
</div>

  </main>  <footer>
    <div class="card" style="padding:12px">
      <p class="small">© ProcuraJá — Desenvolvido como demo. Para produção, integrar backend, APIs de pagamento e segurança.</p>
    </div>
  </footer>  <script>
    // Basic multilingual strings
    const strings = {
      pt: {
        heroTitle: 'Anúncios recentes',
        heroSubtitle: 'Encontre casas, carros, empregos e serviços em Angola — destaque para Luanda.',
        howItWorksTitle: 'Como funciona',
        publishTitle: 'Publicar anúncio',
        labelTitle: 'Título',
        labelCategory: 'Categoria',
        labelDesc: 'Descrição',
        labelCity: 'Cidade',
        helpTitle: 'Suporte'
      },
      en: {
        heroTitle: 'Recent ads',
        heroSubtitle: 'Find houses, cars, jobs and services in Angola — highlight: Luanda.',
        howItWorksTitle: 'How it works',
        publishTitle: 'Publish an ad',
        labelTitle: 'Title',
        labelCategory: 'Category',
        labelDesc: 'Description',
        labelCity: 'City',
        helpTitle: 'Support'
      },
      fr: {
        heroTitle: 'Annonces récentes',
        heroSubtitle: 'Trouvez maisons, voitures, emplois et services en Angola — Luanda en vedette.',
        howItWorksTitle: 'Comment ça marche',
        publishTitle: 'Publier une annonce',
        labelTitle: 'Titre',
        labelCategory: 'Catégorie',
        labelDesc: 'Description',
        labelCity: 'Ville',
        helpTitle: 'Assistance'
      },
      zh: {
        heroTitle: '最新广告',
        heroSubtitle: '在安哥拉查找房屋、汽车、工作和服务 — 重点：罗安达。',
        howItWorksTitle: '运作方式',
        publishTitle: '发布广告',
        labelTitle: '标题',
        labelCategory: '类别',
        labelDesc: '描述',
        labelCity: '城市',
        helpTitle: '支持'
      }
    };

    const $ = id => document.getElementById(id);
    const langSel = $('lang');
    function applyLang(l){
      const s = strings[l] || strings.pt;
      $('heroTitle').textContent = s.heroTitle;
      $('heroSubtitle').textContent = s.heroSubtitle;
      $('howItWorksTitle').textContent = s.howItWorksTitle;
      $('publishTitle').textContent = s.publishTitle;
      $('labelTitle').textContent = s.labelTitle;
      $('labelCategory').textContent = s.labelCategory;
      $('labelDesc').textContent = s.labelDesc;
      $('labelCity').textContent = s.labelCity;
      $('helpTitle').textContent = s.helpTitle;
    }
    langSel.addEventListener('change', e=>applyLang(e.target.value));
    applyLang('pt');

    // Ads storage (localStorage demo)
    const ADS_KEY = 'procura_ads_v1';
    function loadAds(){
      return JSON.parse(localStorage.getItem(ADS_KEY) || '[]');
    }
    function saveAds(list){ localStorage.setItem(ADS_KEY, JSON.stringify(list)); }

    function renderAds(filter){
      const list = loadAds();
      const container = $('adsList');
      container.innerHTML = '';
      const filtered = list.filter(a=>!filter || filter==='Todos' || a.category===filter);
      if(filtered.length===0){ container.innerHTML = '<p class="small">Sem anúncios</p>'; return; }
      filtered.reverse().forEach(a=>{
        const div = document.createElement('div'); div.className='ad';
        div.innerHTML = `<h4>${a.title} <small style="float:right;font-weight:600">${a.city||''}</small></h4>
                         <p class="small">${a.category} • ${a.type} • ${a.contact}</p>
                         <p>${a.desc||''}</p>
                         <p class="small">Status: ${a.status||'Pendente'}</p>`;
        container.appendChild(div);
      });
    }
    renderAds();

    // Category buttons
    document.querySelectorAll('.cat button').forEach(b=>b.addEventListener('click',()=>renderAds(b.dataset.cat)));

    // Form handling
    const form = document.getElementById('postForm');
    const adType = document.getElementById('adType');
    const paymentSection = document.getElementById('paymentSection');
    adType.addEventListener('change',()=>{ paymentSection.style.display = adType.value==='paid'?'block':'none'; });

    form.addEventListener('submit',async e=>{
      e.preventDefault();
      const fd = new FormData(form);
      const ad = {
        id: 'AD-'+Date.now(),
        title: fd.get('title'),
        category: fd.get('category'),
        desc: fd.get('desc'),
        city: fd.get('city')||'Luanda',
        type: fd.get('type'),
        amount: fd.get('amount')||'',
        contact: fd.get('contact'),
        status: fd.get('type')==='paid'?'Aguardando pagamento':'Pendente',
        createdAt: new Date().toISOString()
      };

      // handle receipt file (convert to base64, demo only)
      const file = fd.get('receipt');
      if(file && file.size>0){
        ad.receiptName = file.name;
        ad.receiptData = await toBase64(file);
      }

      const list = loadAds(); list.push(ad); saveAds(list);
      alert('Anúncio submetido! Se foi pago, envia o comprovativo e nós validamos.');
      form.reset(); paymentSection.style.display='none'; renderAds();
    });

    function toBase64(file){ return new Promise((res,rej)=>{
      const r = new FileReader(); r.onload = ()=>res(r.result); r.onerror=rej; r.readAsDataURL(file);
    }); }

    // Admin (simple client-side authentication)
    $('openAdmin').addEventListener('click',()=>{
      const pw = prompt('Admin password:');
      if(pw==='admin123') openAdminPanel(); else alert('Senha incorreta');
    });

    function openAdminPanel(){
      const overlay = document.createElement('div'); overlay.style.position='fixed';overlay.style.left=0;overlay.style.top=0;overlay.style.right=0;overlay.style.bottom=0;overlay.style.background='rgba(2,6,23,0.6)';overlay.style.display='flex';overlay.style.alignItems='center';overlay.style.justifyContent='center';
      const box = document.createElement('div'); box.style.width='900px'; box.style.maxHeight='80vh'; box.style.overflow='auto'; box.className='card';
      box.innerHTML = `<div style="display:flex;justify-content:space-between;align-items:center"><h3>Admin - Pagamentos pendentes</h3><div><button id="closeAdmin" class="btn-ghost">Fechar</button></div></div><div id="adminList"></div>`;
      overlay.appendChild(box); document.body.appendChild(overlay);
      $('closeAdmin').addEventListener('click',()=>overlay.remove());
      renderAdminList();
    }

    function renderAdminList(){
      const adminList = document.getElementById('adminList');
      const list = loadAds().filter(a=>a.status!=='Aprovado');
      if(list.length===0){ adminList.innerHTML='<p class="small">Nenhum pagamento pendente.</p>'; return; }
      adminList.innerHTML='';
      list.forEach(a=>{
        const d = document.createElement('div'); d.style.borderBottom='1px solid #eef6ff'; d.style.padding='10px 0';
        d.innerHTML = `<strong>${a.title}</strong> <span class="small">(${a.category} • ${a.city})</span><p class="small">Status: ${a.status}</p>
                       <p>${a.desc||''}</p>
                       <p class="small">Contato: ${a.contact} • Valor: ${a.amount||'—'}</p>`;
        if(a.receiptData){
          const v = document.createElement('div'); v.style.marginTop='8px'; v.innerHTML=`<a href="${a.receiptData}" target="_blank">Ver comprovativo (${a.receiptName})</a>`; d.appendChild(v);
        }
        const approve = document.createElement('button'); approve.className='btn'; approve.style.marginTop='8px'; approve.textContent='Aprovar';
        const reject = document.createElement('button'); reject.className='btn-ghost'; reject.style.marginLeft='8px'; reject.textContent='Rejeitar';
        approve.addEventListener('click',()=>{ updateStatus(a.id,'Aprovado'); renderAdminList(); renderAds(); });
        reject.addEventListener('click',()=>{ if(confirm('Rejeitar anúncio?')){ updateStatus(a.id,'Rejeitado'); renderAdminList(); renderAds(); }});
        d.appendChild(approve); d.appendChild(reject);
        adminList.appendChild(d);
      });
    }

    function updateStatus(id,status){ const list=loadAds(); const idx=list.findIndex(x=>x.id===id); if(idx>-1){ list[idx].status=status; saveAds(list); } }

    // Search
    $('searchBtn').addEventListener('click',()=>{
      const q = $('searchInput').value.toLowerCase(); const list = loadAds(); const filtered = list.filter(a=>a.title.toLowerCase().includes(q) || (a.desc||'').toLowerCase().includes(q)); renderAds(); // simple
      // show only matches
      const container = $('adsList'); container.innerHTML=''; filtered.reverse().forEach(a=>{ const div=document.createElement('div'); div.className='ad'; div.innerHTML=`<h4>${a.title} <small style="float:right;font-weight:600">${a.city||''}</small></h4><p class="small">${a.category} • ${a.type} • ${a.contact}</p><p>${a.desc||''}</p><p class="small">Status: ${a.status||'Pendente'}</p>`; container.appendChild(div); });
    });
  </script></body>
</html>
