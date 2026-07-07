MAPr — Mapa de Processos e Indicadores
Ferramenta de estruturação teórica e monitoramento de indicadores para políticas públicas, construída sobre a metodologia MAPr (Jannuzzi, 2016) e a Ficha de Documentação do Indicador do Guia Referencial da ENAP (Bahia, 2021).
Este projeto nasceu para o Selo Biocombustível Social (SBS), mas o motor (`index.html`) é genérico: qualquer política com seu próprio `estrutura.json` pode reutilizá-lo.
---
Arquivos do projeto
Arquivo	O que é
`index.html`	O motor da aplicação — HTML, CSS e JavaScript num único arquivo. Não tem conteúdo específico do SBS embutido; ele só sabe ler e renderizar o que estiver em `estrutura.json` e `indicadores.json`.
`estrutura.json`	O conteúdo do Mapa de Processos (MAPr) da política — níveis, blocos aplicáveis, grupos temáticos e itens.
`indicadores.json`	Os indicadores já cadastrados, cada um vinculado a um bloco (e, opcionalmente, a um grupo) de um nível do `estrutura.json`.
---
Funcionalidades do app
O app tem três modos, alternáveis pelos botões do topo:
📊 Indicadores — a trilha do MAPr com marcadores coloridos por status de maturidade. Clique num marcador para abrir a ficha do indicador na aba lateral. Use a seta ao lado de cada grupo para expandir/recolher seus itens, ou o botão "Expandir tudo" para abrir todos de uma vez.
🛠️ Estruturação — por padrão mostra a estrutura em modo leitura (mesmo visual de "Indicadores", sem marcadores). Clique em "✏️ Editar estrutura" para tornar tudo editável: nomes de níveis, blocos aplicáveis por nível, grupos temáticos e itens. Um nível sozinho não é rotulado "Nível 1" — a numeração só aparece quando há dois ou mais níveis. Use "+ Adicionar nível" para criar novos níveis (todos os cinco blocos vêm aplicáveis por padrão; desmarque os que não se aplicam).
📖 Teoria do Programa — um campo de texto livre (Markdown simples). Por padrão mostra a versão formatada para leitura; clique em "✏️ Editar" para editar o texto bruto, e no mesmo botão para voltar à leitura formatada.
Cada indicador recebe automaticamente um identificador (IN01, IN02, ...) usado nas exportações, atribuído pela posição do indicador na estrutura do MAPr — não precisa ser preenchido manualmente.
Exportações
PDF: a Teoria do Programa aparece primeiro, depois o Mapa de Processos (blocos como colunas horizontais lado a lado, com os grupos e itens completos abaixo de cada coluna), e por fim os Indicadores — primeiro um índice por bloco (código, nome e descrição), depois a ficha completa de cada indicador, com uma marca da cor de maturidade. Use o botão "🖨 PDF" e, na caixa de impressão do navegador, escolha "Salvar como PDF".
Markdown: segue a mesma ordem (Teoria → MAPr → Indicadores), em seções bem separadas — os indicadores não aparecem misturados com os descritores do MAPr.
Como abrir e testar
Importante: por uma restrição de segurança dos navegadores (política de CORS), não é possível abrir o `index.html` por duplo-clique direto e esperar que ele carregue os arquivos JSON automaticamente — o navegador bloqueia essa leitura quando a página é aberta via `file://`. Isso não é um defeito do app; é assim para qualquer site estático.
Opção 1 — Testar localmente com um servidor simples
Se você tem Python instalado (a maioria dos computadores tem), abra o terminal na pasta do projeto e rode:
```bash
python3 -m http.server 8000
```
Depois acesse `http://localhost:8000` no navegador.
Opção 2 — Extensão "Live Server" (VS Code)
Se você usa o VS Code, instale a extensão "Live Server", clique com o botão direito no `index.html` e escolha "Open with Live Server".
Opção 3 — GitHub Pages (produção)
No GitHub Pages, esse problema não existe — a página já roda em `https://`, então o carregamento automático dos JSONs funciona normalmente, sem nenhum passo extra.
---
Publicando no GitHub Pages
Crie um repositório no GitHub e envie os três arquivos (`index.html`, `estrutura.json`, `indicadores.json`) para a raiz dele.
Em Settings → Pages, escolha a branch (geralmente `main`) e a pasta raiz (`/`).
Aguarde alguns minutos — o GitHub fornecerá uma URL do tipo `https://SEUUSUARIO.github.io/NOMEDOREPO/`.
Reutilizando para outra política
Este repositório pode ser marcado como "Template repository" (em Settings → General → Template repository). Isso permite que qualquer pessoa crie uma cópia completa do projeto com um clique (botão "Use this template"), bastando depois substituir o conteúdo de `estrutura.json` pelo MAPr da nova política — o motor (`index.html`) não precisa de nenhuma alteração.
---
Editando os indicadores em grupo (modo colaborativo)
O app permite que qualquer pessoa com o link edite os indicadores diretamente pela interface, salvando as alterações de volta no repositório GitHub através da API do GitHub. Não existe um "botão de ativar API" — a API REST do GitHub já está sempre disponível para qualquer repositório. O que precisa ser feito é gerar a credencial (token) que autoriza o app a usá-la em nome de vocês. Siga o passo a passo abaixo.
Passo 1 — Publicar o repositório
Antes de gerar o token, os três arquivos (`index.html`, `estrutura.json`, `indicadores.json`) precisam estar num repositório do GitHub (veja "Publicando no GitHub Pages" acima).
Passo 2 — Gerar o token de acesso
No GitHub, clique na sua foto de perfil (canto superior direito) → Settings.
No menu lateral esquerdo, role até o final e clique em Developer settings.
Clique em Personal access tokens → Fine-grained tokens.
Clique em Generate new token.
Dê um nome qualquer ao token (ex.: "MAPr SBS — edição").
Em Repository access, escolha "Only select repositories" e selecione apenas o repositório do MAPr — nunca marque "All repositories".
Desça até Permissions → Repository permissions, encontre "Contents" e mude de "No access" para "Read and write". Nenhuma outra permissão é necessária.
Em Expiration, escolha o prazo máximo permitido (atualmente 1 ano). O GitHub não permite tokens sem expiração — quando vencer, basta gerar outro em menos de um minuto.
Clique em Generate token, no final da página.
Copie o token imediatamente — ele só é exibido uma vez; se você sair da página sem copiar, terá que gerar outro.
Passo 3 — Usar o token no app
Abra o app publicado, clique em "⚙️ Conectar GitHub" no topo, e preencha:
Usuário/organização: o nome da conta dona do repositório.
Repositório: o nome do repositório (sem a parte do usuário).
Branch: geralmente `main`.
Token: cole o token copiado no Passo 2.
Clique em Conectar. O app buscará a versão mais recente dos arquivos diretamente do GitHub.
Como usar o token no app
Clique em "⚙️ Conectar GitHub", no topo da página.
Preencha usuário/organização, nome do repositório, branch e cole o token.
O token não é salvo em nenhum lugar — nem em cookies, nem em localStorage, nem no código da página. Ele fica só na memória do navegador durante aquela sessão, e desaparece ao fechar a aba. Isso significa que cada pessoa (ou o grupo, se decidirem compartilhar o mesmo token) precisa colá-lo novamente sempre que abrir a página para editar.
Por que isso importa: qualquer código que roda no navegador de uma página pública pode ser inspecionado por quem souber onde olhar ("Ver código-fonte" ou "Inspecionar"). Se o token estivesse embutido no HTML, ficaria exposto publicamente. Colando-o a cada sessão, o token nunca aparece no código nem fica salvo em disco.
Sobre o risco
Como os dados deste projeto não são sensíveis (não há informação pessoal, sigilosa ou de segurança), o pior cenário realista em caso de uso indevido do token é alguém alterar ou apagar conteúdo do arquivo de indicadores — e isso é totalmente reversível pelo histórico de commits do próprio Git (`git revert` ou reverter pela interface do GitHub). Por isso, um único token compartilhado pela equipe, com o cuidado de nunca embuti-lo no código, é uma escolha razoável para este caso de uso.
Se em algum momento o token for exposto ou comprometido, revogue-o imediatamente em Settings → Developer settings → Personal access tokens e gere um novo.
---
Exportação
Markdown: gera um `.md` com toda a estrutura do MAPr e os indicadores já alocados em seus blocos/grupos — pronto para colar num documento ou anexar ao SEI.
PDF: usa a função de impressão do próprio navegador (Ctrl+P / Cmd+P), com uma folha de estilos dedicada que oculta os elementos de interface e mostra só o conteúdo. Ao imprimir, escolha "Salvar como PDF" no destino da impressão.
---
Estrutura de dados — referência rápida
`estrutura.json`
```
{
  "politica": "...",
  "metodologia": "MAPr (Jannuzzi, 2016)",
  "niveis": [
    {
      "id": "nivel1",
      "nome": "...",
      "blocos_aplicaveis": ["recursos", "atividades", "produtos"],
      "grupos_por_bloco": {
        "produtos": [
          { "id": "...", "nome": "...", "itens": [{ "id": "...", "texto": "..." }] }
        ]
      }
    }
  ]
}
```
Os cinco blocos (Recursos, Atividades, Produtos, Resultados, Impactos) são um vocabulário fixo embutido no motor — não são editáveis por conteúdo, só a aplicabilidade de cada um a cada nível.
`indicadores.json`
```
{
  "indicadores": [
    {
      "id": "...",
      "nome": "...",
      "status_maturidade": "existente_monitorado | exigido_pendente | aspiracional",
      "vinculos": [
        { "nivel_id": "nivel2", "bloco_id": "produtos", "grupo_id": null }
      ],
      "...demais campos da ficha (ver Guia ENAP, 2021)"
    }
  ]
}
```
Um indicador pode ter múltiplos vínculos (útil para indicadores compostos, como o PMEN, que atravessam mais de um grupo ou nível).
