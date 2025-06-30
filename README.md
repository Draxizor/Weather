<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Prédio Mix</title>
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Roboto', sans-serif;
            background: linear-gradient(135deg, #f0f4f8, #ffffff);
            color: #333;
            height: 100vh;
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }

        .container {
            display: flex;
            flex-direction: column;
            padding: 20px;
            flex-grow: 1;
        }

        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px 20px;
            background-color: #004466;
            color: #fff;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
        }

        .header-left h1 {
            font-size: 2em;
            font-weight: 700;
        }

        .header-left p {
            font-size: 0.95em;
            opacity: 0.9;
        }

        .clima-widget {
            display: flex;
            flex-direction: column;
            align-items: flex-end;
            background: #ffffff22;
            padding: 10px;
            border-radius: 10px;
            font-size: 0.9em;
        }

        .main-content {
            margin-top: 20px;
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 20px;
            flex-grow: 1;
        }

        .section {
            padding: 20px;
            border-radius: 16px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.05);
            transition: transform 0.3s ease;
        }

        .section:hover {
            transform: scale(1.02);
        }

        .activities {
            background: linear-gradient(135deg, #fefcea, #f1da36);
        }

        .internal-news {
            background: linear-gradient(135deg, #ffecd2, #fcb69f);
        }

        .campaign-a {
            background: linear-gradient(135deg, #a1c4fd, #c2e9fb);
        }

        .campaign-b {
            background: linear-gradient(135deg, #d4fc79, #96e6a1);
        }

        .general-news {
            background: linear-gradient(135deg, #fbc2eb, #a6c1ee);
            overflow-y: auto;
        }

        .section h2 {
            font-size: 1.3em;
            color: #004466;
            margin-bottom: 10px;
        }

        .news-item {
            margin-bottom: 10px;
        }

        .news-title {
            font-weight: bold;
        }

        .news-time {
            font-size: 0.8em;
            color: #555;
        }

        .campaign-content {
            text-align: center;
            margin-top: 10px;
        }

        @media (max-width: 768px) {
            .header {
                flex-direction: column;
                text-align: center;
                gap: 10px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header class="header">
            <div class="header-left">
                <h1>Prédio Mix</h1>
                <p id="current-date">Carregando data...</p>
            </div>
            <div class="clima-widget" id="clima-widget">
                <span id="cidade-clima"></span>
                <span id="icone-clima"></span>
                <span id="temperatura">--°C</span>
                <span id="descricao"></span>
            </div>
        </header>
        <main class="main-content">
            <div class="section activities">
                <h2>Atividades</h2>
                <p>10/07 – auditoria interna</p>
                <p>11/07 – Aniversário fulano</p>
            </div>
            <div class="section internal-news">
                <h2>Notícia interna</h2>
                <div id="noticias-internas">Carregando...</div>
            </div>
            <div class="section campaign-a">
                <h2>Foto campanha A</h2>
                <div class="campaign-content">
                    <p>Campanha de conscientização ambiental</p>
                </div>
            </div>
            <div class="section campaign-b">
                <h2>Foto campanha B</h2>
                <div class="campaign-content">
                    <p>Campanha de segurança no trabalho</p>
                </div>
            </div>
            <div class="section general-news">
                <h2>Notícias gerais</h2>
                <div id="noticias-gerais">Carregando...</div>
            </div>
        </main>
    </div>

    <script>
        const API_KEY = '197f335da220672a9c2209b98be31fd2';
        const CIDADE = 'Limeira';
        const NEWS_API_KEY = 'c932f985ff704a8abed52d04da430321';

        async function buscarClima() {
            try {
                const res = await fetch(`https://api.openweathermap.org/data/2.5/weather?q=${CIDADE}&appid=${API_KEY}&units=metric&lang=pt_br`);
                const data = await res.json();

                document.getElementById('cidade-clima').textContent = data.name;
                document.getElementById('temperatura').textContent = `${Math.round(data.main.temp)}°C`;
                document.getElementById('descricao').textContent = data.weather[0].description;
                document.getElementById('icone-clima').textContent = '☀️';
            } catch (e) {
                console.error('Erro clima:', e);
            }
        }

        async function buscarNoticias() {
            try {
                const res = await fetch(`https://newsapi.org/v2/top-headlines?country=br&pageSize=5&apiKey=${NEWS_API_KEY}`);
                const data = await res.json();
                const container = document.getElementById('noticias-gerais');
                container.innerHTML = '';
                data.articles.forEach(noticia => {
                    const item = document.createElement('div');
                    item.className = 'news-item';
                    item.innerHTML = `
                        <div class="news-title">${noticia.title}</div>
                        <div class="news-time">${new Date(noticia.publishedAt).toLocaleString('pt-BR')}</div>
                    `;
                    container.appendChild(item);
                });
            } catch (e) {
                console.error('Erro notícias:', e);
            }
        }

        function atualizarData() {
            const agora = new Date();
            const opcoes = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
            const dataFormatada = agora.toLocaleDateString('pt-BR', opcoes);
            document.getElementById('current-date').textContent = dataFormatada.charAt(0).toUpperCase() + dataFormatada.slice(1);
        }

        document.addEventListener('DOMContentLoaded', () => {
            atualizarData();
            buscarClima();
            buscarNoticias();
        });
    </script>
</body>
</html>
