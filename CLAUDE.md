# Super Racha (projeto "Racha dos Amigos")

Arquivo principal: `Racha dos Amigos.dc.html` (um único DC com todas as páginas).

## Regras do usuário
- Racha sempre às terças, 20h. Datas conforme calendário real.
- Mudanças pontuais: não alterar layout, design ou outras páginas sem pedido.
- Estilo: fundo escuro (#0f1412), verde (#22c55e / #4ade80), fontes Barlow + Barlow Condensed.
- Logo no cabeçalho: https://i.imgur.com/nw1WEk5.png ("Super Racha").
- Uniformes/coletes (só 2): PNG sem fundo em `assets/colete-verde.png` e `assets/colete-preto.png` (recortados de fDP8278.jpeg / YhLya7f.png; proporção 310/480). Padrão A/C verde, B/D preto (configurável).
- Logos: Super Connect (verde) https://i.imgur.com/uChSEmY.png → A/C; Super Security (preto) https://i.imgur.com/dEvqbx7.png → B/D. MISVhzu.jpeg = foto do campo (referência); jwM10k5.jpeg = print de app (só referência).
- Evitar `<img src="{{ hole }}">` (gera erro de console); usar background-image com hole.
- Imagens (coletes, logos Super Connect/Security, logo do cabeçalho) estão EMBUTIDAS no arquivo como data URI webp (static SHIRTS/IMG_CONNECT/IMG_SECURITY) para funcionar fora do preview. Config guarda chave 'connect'/'security' (URLs imgur antigas mapeadas via LEGACY).

## Estado atual (sem banco de dados, tudo em localStorage)
- Navegação: Início, Jogadores, Presença, Times, ⚽ Partida, Coletes, Histórico, Ranking, Configurações (key `racha_pagina`).
- Coletes (`racha_coletes_v1`): [{id, date YYYY-MM-DD (terça), verde: pid, preto: pid, dev:{verde,preto}}]. Mostra com quem está agora + registro por racha.
- Celular (<640px): campo da Partida vira vertical (time 1 em cima, time 2 embaixo); state.narrow.
- Jogadores (`racha_jogadores_v2`): nome, apelido, foto, número, capitão, posição (goleiro/linha), setor (auto/defesa/meio/ataque), nota 1–10 passo 0,5 (sincroniza habilidade), status. Estatísticas NÃO são armazenadas no jogador: derivadas do histórico (careerStats).
- Presença (`racha_presenca_v1`): importação WhatsApp.
- Times (`racha_times_v1`): geração automática em linhas de 6 (1 goleiro + 5), sobra vira próxima linha (C, D… até F). ≤12 jogadores = 2 times iguais. Critérios: tamanho → goleiros (A/B primeiro) → nota média → aleatório. 🔒 fixar. gen = {k, sizes, teams, locks}.
- Partida (`racha_partida_v1`): phase setup/live/paused/ended/done; startTs, pausedAt, pausedMs, endTs (cronômetro recalculado por timestamps); rule tempogols/tempo/gols/livre + minutos (1–60) + golsMax (1–3), endReason tempo|gols; apito via WebAudio (whistle()) ao acabar; events [{id,type goal|yellow|red|foul|start|pause|resume|end, team, player(id), assist, ms}]; rosters; pos (posições arrastadas, chave L/R+id); histId.
- Histórico (`racha_partidas_hist_v1`): snapshot completo da partida (events, rosters, durMs, a/b). Eventos editáveis também no histórico.
- Config (`racha_config_v1`): uniforme e logo por time. Backup exportar/importar JSON.
- Banco online: Firebase Realtime Database via REST + EventSource (sem SDK). URL lida de `racha-config.json` ({firebaseUrl}) no GitHub Pages, senão localStorage `racha_cloud_url`. Chaves SYNC guardadas como string JSON em /racha/<key>. Senha admin (hash SHA-256) em `racha_admin_v1`; sem senha todos editam; com senha, visitantes só visualizam (write() bloqueia). Só admin roda checkEnd; visitantes ouvem apito ao receber phase 'ended'.
- Publicação: `index.html` = bundle (super_inline_html) de `Racha dos Amigos.dc.html`; subir index.html + racha-config.json na raiz do repo `superracha` (GitHub Pages).
- Ranking: real, derivado do histórico (V=3, E=1).
- Resumo (aba 'resumo'): modo dia (Equipe que mais venceu, share PNG) e mensal (Artilheiro 👑 e Líder de assistências ⭐, empates mostram todos; share PNG). Derivado do histórico.
- Foto do jogador: editor de recorte (canvas 360×480, guia do topo da cabeça a 14%); remoção de fundo via MediaPipe (CDN, só online); fallback PNG recortado. fotoFramed=true posiciona coroa dentro da folga acima da cabeça.
- Gol: autor obrigatório; "Sem assistência / anotar depois"; botão "+ 🅰 Assist." na timeline (partida e histórico).
- Início: Próximo racha e Confirmados ainda demonstrativos; Partida atual e Placar ao vivo.
