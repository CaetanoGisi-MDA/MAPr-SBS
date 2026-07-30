# MAPr — Mapa de Processos e Indicadores

Ferramenta de estruturação teórica e monitoramento de indicadores para políticas públicas, construída sobre a metodologia MAPr (Jannuzzi, 2016) e a Ficha de Documentação do Indicador do Guia Referencial da ENAP (Bahia, 2021).

Este projeto nasceu para o Selo Biocombustível Social (SBS), mas o motor (`index.html`) é genérico: qualquer política com seu próprio `estrutura.json` pode reutilizá-lo.

---

## Arquivos do projeto

| Arquivo | O que é |
|---|---|
| `index.html` | O motor da aplicação — HTML, CSS e JavaScript num único arquivo. Não tem conteúdo específico do SBS embutido; ele só sabe ler e renderizar o que estiver nos três arquivos de dados abaixo. |
| `estrutura.json` | Só o conteúdo do Mapa de Processos (MAPr) da política — níveis, blocos aplicáveis, grupos temáticos e itens. |
| `indicadores.json` | Os indicadores já cadastrados, cada um vinculado a um bloco (e, opcionalmente, a um grupo) de um nível do `estrutura.json`. |
| `documentos.json` | O conteúdo textual narrativo da política — a Teoria do Programa e o Plano de Monitoramento e Avaliação — mais o retrato de sincronia usado para avisar quando o texto do Plano ficou desatualizado em relação aos indicadores cadastrados. |

---

## Funcionalidades do app

O app tem três abas, alternáveis pelos botões do topo, na ordem em que aparecem:

- **📖 Teoria do Programa** — um campo de texto livre (Markdown simples). Por padrão mostra a versão formatada para leitura; clique em **"✏️ Editar"** para editar o texto bruto, e no mesmo botão para voltar à leitura formatada.
- **🗺️ MAPr e Indicadores** — uma única aba que reúne a estrutura do Mapa de Processos e o cadastro de indicadores. Por padrão, mostra a trilha em modo leitura, com os marcadores coloridos por status de maturidade — clique num marcador para abrir a ficha do indicador na aba lateral, ou use a seta ao lado de cada grupo (ou o botão "Expandir tudo") para ver os itens. Clique em **"✏️ Editar MAPr"** para trocar para o modo de edição da estrutura em si: nomes de níveis, blocos aplicáveis, grupos temáticos e itens — nesse modo os marcadores ficam ocultos, já que a edição de estrutura e a gestão de indicadores não acontecem ao mesmo tempo. Um nível sozinho não é rotulado "Nível 1" — a numeração só aparece quando há dois ou mais níveis. Use **"+ Adicionar nível"** para criar novos níveis. Só na **exportação** (PDF/Markdown) o Mapa de Processos e a Matriz de Indicadores voltam a ser duas seções distintas — ver "Exportações" abaixo.
- **📅 Plano de M&A** — outro campo de texto livre (Markdown simples, com suporte a **listas com marcadores** e **tabelas**, além de títulos e negrito), para o Plano de Monitoramento e Avaliação. Mesmo padrão de edição/leitura da Teoria. Descrito em detalhe na seção seguinte.

Cada indicador recebe automaticamente um identificador (**IN01**, **IN02**, ...) usado nas exportações e na tabela copiável do Plano, atribuído pela posição do indicador na estrutura do MAPr — não precisa ser preenchido manualmente.

### O Plano de M&A e a sincronia com a Matriz de Indicadores

O texto do Plano é editado livremente — inclusive a tabela da "matriz de indicadores" que normalmente aparece na sua Seção 4 é só texto Markdown como qualquer outro trecho, **não é gerada nem substituída automaticamente**. Isso evita que uma automação sobrescreva comentários ou reformulações que vocês tenham feito ao redor da tabela.

Só que, como o texto é digitado à mão, ele pode ficar desatualizado se algum indicador for criado, removido ou alterado depois da última revisão do Plano. Para isso, o app mantém um pequeno "retrato" (quantidade e lista de indicadores) tirado no momento em que alguém confirma que o texto está sincronizado. Sempre que esse retrato não bater mais com o `indicadores.json` atual, um aviso aparece no topo da aba do Plano — e continua aparecendo, mesmo em sessões futuras, até alguém agir. Duas ações resolvem o aviso:

- **📋 Copiar tabela da Matriz** — gera uma tabela em Markdown (Indicador | Vínculo no mapa de processos | Status), já formatada exatamente como a Seção 4 original, e copia para a área de transferência. Cabe a quem estiver editando colar onde achar melhor no texto e ajustar a redação ao redor.
- **✓ Confirmar sincronia** — atualiza o retrato para o estado atual dos indicadores e faz o aviso sumir, sem alterar o texto. Use depois de colar a tabela nova (ou depois de decidir, por julgamento próprio, que o texto atual já reflete a mudança).

### A capa do documento

Clique no **título do app** (canto superior esquerdo — o nome da política) para abrir a janela de edição da capa. Ela controla quatro coisas:

- **Cabeçalho** — texto livre, com quebra de linha, que aparece no topo da capa. Vem pré-preenchido com a cadeia institucional (Ministério → Secretaria → Departamento → Coordenação), mas é totalmente editável.
- **Nome da Política** — este é o **mesmo campo** que já existe (`estrutura.politica`): editar aqui também atualiza o título do app e a primeira linha do Markdown exportado. Não há duplicação de dado.
- **Subtítulo** — aparece em itálico, logo abaixo do nome, no centro da capa.
- **Data** — só exibida, não editável. Mês por extenso + ano (ex. "Julho de 2026"), calculada automaticamente sempre que a janela abre e de novo no momento real de cada exportação — nunca fica "congelada" num valor antigo.

### Exportações

A exportação é feita por um **seletor único** no topo (PDF ou Markdown) seguido do botão **"Exportar"**.

- **PDF**: a primeira página é a **capa** (cabeçalho no topo, nome da política e subtítulo centralizados, data no rodapé), seguida de uma página de **índice** com links clicáveis para as quatro seções (e, dentro da Matriz de Indicadores, para o Índice por bloco e as Fichas completas). Depois vêm as quatro seções de conteúdo, na ordem Teoria do Programa → Mapa de Processos (blocos como colunas horizontais lado a lado, com os grupos e itens completos abaixo de cada coluna) → Plano de Monitoramento e Avaliação (como seção própria, incluindo tabelas e listas do texto) → Matriz de Indicadores (índice por bloco com código, nome e descrição, depois a ficha completa de cada indicador, com marca de cor de maturidade). **Capa, índice e cada uma das quatro seções sempre começam numa página nova** — quebra de página forçada antes de cada uma —, não importa onde o conteúdo anterior termine. As páginas de conteúdo não têm cabeçalho nem numeração repetidos — é conteúdo puro, sem nenhuma informação automática de data/hora.

  Ao clicar em "Exportar" com PDF selecionado, abre a caixa de impressão do navegador — escolha "Salvar como PDF" no destino. **Importante:** desmarque a opção "Cabeçalhos e rodapés" (ou "Headers and footers") na própria caixa de impressão antes de salvar — é um recurso do navegador, fora do controle do app, que insere data/hora/endereço automaticamente em cada página se deixado ligado. Um aviso lembra disso na hora de exportar, mas vale saber de antemão.

  Sobre números de página: um índice com números de página reais (como um sumário de livro) depende de o navegador calcular a paginação final antes de gerar o link — recurso que nenhum navegador implementa de forma confiável para impressão nativa. Por isso o índice é **clicável, mas sem número de página** — funciona perfeitamente para navegar dentro do PDF já exportado, só não substitui um sumário remissivo tradicional.

- **Markdown**: mesma lógica adaptada ao formato — cabeçalho, nome, subtítulo e data no topo do arquivo, seguidos de um índice com links internos (`#âncora`, no padrão que leitores de Markdown como GitHub costumam reconhecer automaticamente a partir dos títulos), e então as quatro seções na mesma ordem, com o texto do Plano inserido tal como está (já é Markdown válido, então entra sem conversão). Gera um arquivo `.md` e inicia o download automaticamente.

## Como abrir e testar

**Importante:** por uma restrição de segurança dos navegadores (política de CORS), **não é possível** abrir o `index.html` por duplo-clique direto e esperar que ele carregue os arquivos JSON automaticamente — o navegador bloqueia essa leitura quando a página é aberta via `file://`. Isso não é um defeito do app; é assim para qualquer site estático.

### Opção 1 — Testar localmente com um servidor simples

Se você tem Python instalado (a maioria dos computadores tem), abra o terminal na pasta do projeto e rode:

```bash
python3 -m http.server 8000
```

Depois acesse `http://localhost:8000` no navegador.

### Opção 2 — Extensão "Live Server" (VS Code)

Se você usa o VS Code, instale a extensão "Live Server", clique com o botão direito no `index.html` e escolha "Open with Live Server".

### Opção 3 — GitHub Pages (produção)

No GitHub Pages, esse problema não existe — a página já roda em `https://`, então o carregamento automático dos JSONs funciona normalmente, sem nenhum passo extra.

---

## Publicando no GitHub Pages

1. Crie um repositório no GitHub e envie os quatro arquivos (`index.html`, `estrutura.json`, `indicadores.json`, `documentos.json`) para a raiz dele.
2. Em **Settings → Pages**, escolha a branch (geralmente `main`) e a pasta raiz (`/`).
3. Aguarde alguns minutos — o GitHub fornecerá uma URL do tipo `https://SEUUSUARIO.github.io/NOMEDOREPO/`.

### Reutilizando para outra política

Este repositório pode ser marcado como **"Template repository"** (em Settings → General → Template repository). Isso permite que qualquer pessoa crie uma cópia completa do projeto com um clique (botão "Use this template"), bastando depois substituir o conteúdo de `estrutura.json` pelo MAPr da nova política, esvaziar `indicadores.json` (`{"indicadores": []}`) e `documentos.json` (textos vazios, com `plano_sincronizacao_matriz` zerado) — o motor (`index.html`) não precisa de nenhuma alteração.

---

## Editando os indicadores em grupo (modo colaborativo)

O app permite que qualquer pessoa com o link edite os indicadores diretamente pela interface, salvando as alterações de volta no repositório GitHub através da API do GitHub. **Não existe um "botão de ativar API"** — a API REST do GitHub já está sempre disponível para qualquer repositório. O que precisa ser feito é gerar a credencial (token) que autoriza o app a usá-la em nome de vocês. Siga o passo a passo abaixo.

### Passo 1 — Publicar o repositório

Antes de gerar o token, os quatro arquivos (`index.html`, `estrutura.json`, `indicadores.json`, `documentos.json`) precisam estar num repositório do GitHub (veja "Publicando no GitHub Pages" acima). Se você estiver migrando um repositório que só tinha os dois arquivos de dados originais, não se preocupe: o app cria o `documentos.json` automaticamente na primeira vez que salvar, caso ele ainda não exista.

### Passo 2 — Gerar o token de acesso

1. No GitHub, clique na sua foto de perfil (canto superior direito) → **Settings**.
2. No menu lateral esquerdo, role até o final e clique em **Developer settings**.
3. Clique em **Personal access tokens → Fine-grained tokens**.
4. Clique em **Generate new token**.
5. Dê um nome qualquer ao token (ex.: "MAPr SBS — edição").
6. Em **Repository access**, escolha **"Only select repositories"** e selecione apenas o repositório do MAPr — nunca marque "All repositories".
7. Desça até **Permissions → Repository permissions**, encontre **"Contents"** e mude de "No access" para **"Read and write"**. Nenhuma outra permissão é necessária.
8. Em **Expiration**, escolha o prazo máximo permitido (atualmente 1 ano). O GitHub não permite tokens sem expiração — quando vencer, basta gerar outro em menos de um minuto.
9. Clique em **Generate token**, no final da página.
10. **Copie o token imediatamente** — ele só é exibido uma vez; se você sair da página sem copiar, terá que gerar outro.

### Passo 3 — Usar o token no app

Abra o app publicado, clique em **"⚙️ Conectar GitHub"** no topo, e preencha:
- **Usuário/organização**: o nome da conta dona do repositório.
- **Repositório**: o nome do repositório (sem a parte do usuário).
- **Branch**: geralmente `main` — se errar o nome, o app tenta automaticamente a branch padrão do repositório antes de reportar erro.
- **Token**: cole o token copiado no Passo 2.

Clique em **Conectar**. O app primeiro confirma que o repositório existe e está acessível com aquele token, depois busca a versão mais recente dos arquivos. Se algo falhar, a mensagem de erro agora indica a causa provável (repositório/arquivo não encontrado, token inválido, ou token sem permissão suficiente).

**O token não é salvo em nenhum lugar** — nem em cookies, nem em localStorage, nem no código da página. Ele fica só na memória do navegador durante aquela sessão, e desaparece ao fechar a aba. Isso significa que cada pessoa (ou o grupo, se decidirem compartilhar o mesmo token) precisa colá-lo novamente sempre que abrir a página para editar. Qualquer código que roda no navegador de uma página pública pode ser inspecionado por quem souber onde olhar ("Ver código-fonte" ou "Inspecionar") — se o token estivesse embutido no HTML, ficaria exposto publicamente. Colando-o a cada sessão, ele nunca aparece no código nem fica salvo em disco.

**Observação sobre autoria dos commits:** como o token é compartilhado pela equipe (em vez de um token por pessoa), todos os commits no histórico do Git aparecerão sob a mesma identidade — a conta dona do token. Se um dia for importante rastrear quem editou cada indicador, isso precisaria ser resolvido com um campo próprio dentro do JSON (ex.: "editado por"), não pelo histórico do Git.

### Sobre o risco

Como os dados deste projeto não são sensíveis (não há informação pessoal, sigilosa ou de segurança), o pior cenário realista em caso de uso indevido do token é alguém alterar ou apagar conteúdo do arquivo de indicadores — e isso é **totalmente reversível** pelo histórico de commits do próprio Git (`git revert` ou reverter pela interface do GitHub). Por isso, um único token compartilhado pela equipe, com o cuidado de nunca embuti-lo no código, é uma escolha razoável para este caso de uso.

Se em algum momento o token for exposto ou comprometido, revogue-o imediatamente em **Settings → Developer settings → Personal access tokens** e gere um novo.

### Solução de problemas comuns de conexão

Se a conexão falhar, a mensagem de erro exibida indica a causa mais provável:

| Mensagem | Causa provável | O que checar |
|---|---|---|
| Erro 404 (não encontrado) | Nome de usuário/organização ou repositório digitado errado; ou `estrutura.json`/`indicadores.json` ainda não foram enviados ao repositório | Confira a grafia exata (maiúsculas/minúsculas importam) e se os arquivos estão na raiz do repositório. Note que `documentos.json` ausente **não** gera esse erro — o app cria um vazio automaticamente. |
| Erro 401 (credencial inválida) | Token incorreto, expirado, ou copiado com espaço em branco extra | Gere um token novo e cole com cuidado |
| Erro 403 (acesso negado) | Token sem a permissão "Contents: Read and write" para aquele repositório específico | Revise a permissão do token no GitHub (Passo 2, item 7) |

Se a branch informada no formulário não existir no repositório, o app tenta automaticamente a branch padrão antes de reportar erro — então normalmente não é necessário acertar o nome exato da branch.

---

## Estrutura de dados — referência rápida

### `estrutura.json`

```
{
  "politica": "...",
  "metodologia": "MAPr (Jannuzzi, 2016)",
  "niveis": [
    {
      "id": "nivel1",
      "nome": "...",
      "descricao": "...",
      "blocos_aplicaveis": ["recursos", "atividades", "produtos"],
      "grupos_por_bloco": {
        "produtos": [
          { "id": "...", "nome": "...", "fundamentacao": "justificativa de criação do grupo (opcional)", "itens": [{ "id": "...", "texto": "..." }] }
        ]
      }
    }
  ]
}
```

Os cinco blocos (Recursos, Atividades, Produtos, Resultados, Impactos) são um vocabulário fixo embutido no motor — não são editáveis por conteúdo, só a **aplicabilidade** de cada um a cada nível.

### `documentos.json`

```
{
  "teoria_do_programa": "Texto livre em Markdown simples (##, ###, **negrito**) — problema, hipótese de mudança, mecanismos de transmissão, premissas e pressupostos.",
  "plano_monitoramento_avaliacao": "Texto livre em Markdown simples, agora também com suporte a listas (- item) e tabelas (formato Markdown padrão, com linha separadora de traços).",
  "plano_sincronizacao_matriz": {
    "total_indicadores": 9,
    "ids_indicadores": ["ind_pmen", "ind_agricultores", "..."]
  },
  "capa_cabecalho": "Texto livre com quebra de linha — a cadeia institucional exibida no topo da capa.",
  "capa_subtitulo": "Texto curto exibido em itálico, abaixo do nome da política, no centro da capa."
}
```

O campo `plano_sincronizacao_matriz` não é editado manualmente — ele é atualizado automaticamente pelo app quando alguém clica em "✓ Confirmar sincronia" na aba do Plano, e é comparado contra o `indicadores.json` atual para decidir se o aviso de dessincronia deve aparecer. Os campos `capa_cabecalho` e `capa_subtitulo` são editados pela janela que abre ao clicar no título do app — o "Nome da Política" que aparece junto nessa janela, por sua vez, edita o campo `politica` do `estrutura.json`, não um campo novo.

### `indicadores.json`

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

Um indicador pode ter **múltiplos vínculos** (útil para indicadores compostos, como o PMEN, que atravessam mais de um grupo ou nível).
