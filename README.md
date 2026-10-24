<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>DivulgaEsquerda - Londrina</title>
<style>
  * { box-sizing: border-box; }
  body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    background: #fafafa;
    margin: 0;
    color: #262626;
  }
  header {
    position: sticky; top: 0; z-index: 10;
    background: #fff;
    border-bottom: 1px solid #dbdbdb;
    padding: 12px 16px;
  }
  header h1 { margin: 0; font-size: 20px; text-align: center; font-weight: 700; }
  .header-desc {
    text-align: center;
    font-size: 13px;
    color: #555;
    max-width: 800px;
    margin: 8px auto 0;
    line-height: 1.4;
  }
  .rainbow-bar {
    height: 4px;
    background: linear-gradient(to right, #E40303, #FF8C00, #FFED00, #008026, #004DFF, #750787);
    width: 100%;
  }
  .controls { max-width: 1000px; margin: 0 auto; padding: 16px 16px 0; }

  .search {
    width: 100%;
    padding: 12px 16px;
    border-radius: 8px;
    border: 1px solid #dbdbdb;
    background: #efefef;
    font-size: 14px;
    margin-bottom: 12px;
    outline: none;
  }
  .search:focus { background: #fff; border-color: #b2b2b2; }

  .chips { display: flex; gap: 8px; overflow-x: auto; padding-bottom: 12px; scrollbar-width: none; }
  .chips::-webkit-scrollbar { display: none; }
  .chip {
    flex: 0 0 auto;
    padding: 6px 14px;
    border-radius: 20px;
    background: #efefef;
    border: 1px solid #dbdbdb;
    font-size: 13px;
    cursor: pointer;
    white-space: nowrap;
    transition: all .15s;
  }
  .chip.active { background: #262626; color: #fff; border-color: #262626; }

  .feed {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
    gap: 16px;
    padding: 16px;
    max-width: 1000px;
    margin: 0 auto;
  }
  .card {
    background: #fff;
    border-radius: 12px;
    overflow: hidden;
    box-shadow: 0 1px 3px rgba(0,0,0,.08);
    display: flex;
    flex-direction: column;
    transition: transform .15s, box-shadow .15s;
  }
  .card:hover { transform: translateY(-2px); box-shadow: 0 4px 12px rgba(0,0,0,.12); }

  .card-img {
    width: 100%;
    aspect-ratio: 1 / 1;
    object-fit: cover;
    background: #efefef;
    display: block;
  }
  .card-info { padding: 12px; flex: 1; display: flex; flex-direction: column; }
  .card-name { font-weight: 600; font-size: 15px; margin: 0 0 2px; }
  .card-service { font-size: 13px; color: #737373; margin: 0 0 8px; }
  .card-tag {
    display: inline-block;
    font-size: 11px;
    padding: 2px 8px;
    background: #efefef;
    border-radius: 10px;
    color: #737373;
    margin-bottom: 10px;
    align-self: flex-start;
  }
  .btn-wa {
    margin-top: auto;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 6px;
    background: #25D366;
    color: #fff;
    text-decoration: none;
    padding: 10px;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
  }
  .btn-wa:hover { background: #1ebe57; }

  .empty { grid-column: 1 / -1; text-align: center; padding: 40px; color: #737373; }
</style>
</head>
<body>

<div class="rainbow-bar"></div>
<header>
  <h1>🏳️‍🌈 DivulgaEsquerda</h1>
  <p class="header-desc">Contra o boicote financeiro à pessoas de esquerda, LGBT+, e anti extrema direita. Divulgação gratuita de prestadores de serviços e empreendimentos pró-LGBT em Londrina e região.</p>
</header>

<div class="controls">
  <input type="search" class="search" id="search" placeholder="Buscar por nome, serviço ou bairro...">
  <div class="chips" id="chips"></div>
</div>

<div class="feed" id="feed"></div>

<script>
  // =====================================================================
  // EDITE AQUI A LISTA DE SERVIÇOS DE LONDRINA E REGIÃO
  // → whatsapp: só números, com 55 (Brasil) + 43 (DDD Londrina)
  // =====================================================================
  const servicos = [
    {
      nome: "Maria Silva",
      servico: "Manicure e pedicure",
      bairro: "Centro",
      categoria: "Beleza",
      whatsapp: "5543999999999",
      foto: "https://picsum.photos/seed/manicure/400/400"
    },
    {
      nome: "João Santos",
      servico: "Eletricista",
      bairro: "Jardim Higienópolis",
      categoria: "Reformas",
      whatsapp: "5543988888888",
      foto: "https://picsum.photos/seed/eletricista/400/400"
    },
    {
      nome: "Ana Costa",
      servico: "Bolos e doces caseiros",
      bairro: "Vila Nova",
      categoria: "Comida",
      whatsapp: "5543977777777",
      foto: "https://picsum.photos/seed/bolos/400/400"
    },
    {
      nome: "Carlos Oliveira",
      servico: "Aulas de violão",
      bairro: "Gleba Palhano",
      categoria: "Aulas",
      whatsapp: "5543966666666",
      foto: "https://picsum.photos/seed/violao/400/400"
    },
    {
      nome: "Lucas Andrade",
      servico: "Psicólogo (Atendimento online e presencial)",
      bairro: "Centro",
      categoria: "Saúde",
      whatsapp: "5543955555555",
      foto: "https://picsum.photos/seed/psico/400/400"
    }
  ];
  // =====================================================================

  const feed = document.getElementById('feed');
  const searchInput = document.getElementById('search');
  const chipsContainer = document.getElementById('chips');
  let categoriaAtiva = 'Todos';

  const categorias = ['Todos', ...new Set(servicos.map(s => s.categoria))];
  categorias.forEach(cat => {
    const chip = document.createElement('button');
    chip.className = 'chip' + (cat === 'Todos' ? ' active' : '');
    chip.textContent = cat;
    chip.onclick = () => {
      categoriaAtiva = cat;
      document.querySelectorAll('.chip').forEach(c => c.classList.remove('active'));
      chip.classList.add('active');
      render();
    };
    chipsContainer.appendChild(chip);
  });

  function render() {
    const busca = searchInput.value.toLowerCase().trim();
    feed.innerHTML = '';

    const filtrados = servicos.filter(s => {
      const okCat = categoriaAtiva === 'Todos' || s.categoria === categoriaAtiva;
      const okBusca = !busca ||
        s.nome.toLowerCase().includes(busca) ||
        s.servico.toLowerCase().includes(busca) ||
        s.bairro.toLowerCase().includes(busca);
      return okCat && okBusca;
    });

    if (filtrados.length === 0) {
      feed.innerHTML = '<p class="empty">Nenhum serviço encontrado 😕</p>';
      return;
    }

    filtrados.forEach(s => {
      const msg = encodeURIComponent(`Olá ${s.nome}! Vi seu serviço no site DivulgaEsquerda e gostaria de saber mais.`);
      const card = document.createElement('div');
      card.className = 'card';
      card.innerHTML = `
        <img class="card-img" src="${s.foto}" alt="${s.servico}" loading="lazy">
        <div class="card-info">
          <span class="card-tag">${s.categoria}</span>
          <p class="card-name">${s.nome}</p>
          <p class="card-service">${s.servico} • ${s.bairro}</p>
          <a class="btn-wa" href="https://wa.me/${s.whatsapp}?text=${msg}" target="_blank">
            💬 Chamar no WhatsApp
          </a>
        </div>
      `;
      feed.appendChild(card);
    });
  }

  searchInput.addEventListener('input', render);
  render();
</script>

</body>
</html>
