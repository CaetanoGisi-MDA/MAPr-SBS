# MAPr — Mapa de Processos e Indicadores

Ferramenta de estruturação teórica e monitoramento de indicadores para políticas públicas, construída sobre a metodologia MAPr (Jannuzzi, 2016) e a Ficha de Documentação do Indicador do Guia Referencial da ENAP (Bahia, 2021).

Este projeto nasceu para o Selo Biocombustível Social (SBS), mas o motor (`index.html`) é genérico: qualquer política com seu próprio `estrutura.json` pode reutilizá-lo.

---

## Arquivos do projeto

| Arquivo | O que é |
|---|---|
| `index.html` | O motor da aplicação — HTML, CSS e JavaScript num único arquivo. Não tem conteúdo específico do SBS embutido; ele só sabe ler e renderizar o que estiver em `estrutura.json` e `indicadores.json`. |
| `estrutura.json` | O conteúdo do Mapa de Processos (MAPr) da política — níveis, blocos aplicáveis, grupos temáticos e itens. |
| `indicadores.json` | Os indicadores já cadastrados, cada um vinculado a um bloco (e, opcionalmente, a um grupo) de um nível do `estrutura.json`. |

---

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

1. Crie um repositório no GitHub e envie os três arquivos (`index.html`, `estrutura.json`, `indicadores.json`) para a raiz dele.
2. Em **Settings → Pages**, escolha a branch (geralmente `main`) e a pasta raiz (`/`).
3. Aguarde alguns minutos — o GitHub fornecerá uma URL do tipo `https://SEUUSUARIO.github.io/NOMEDOREPO/`.

### Reutilizando para outra política

Este repositório pode ser marcado como **"Template repository"** (em Settings → General → Template repository). Isso permite que qualquer pessoa crie uma cópia completa do projeto com um clique (botão "Use this template"), bastando depois substituir o conteúdo de `estrutura.json` pelo MAPr da nova política — o motor (`index.html`) não precisa de nenhuma alteração.

---

## Editando os indicadores em grupo (modo colaborativo)

O app permite que qualquer pessoa com o link edite os indicadores diretamente pela interface, salvando as alterações de volta no repositório GitHub através da API do GitHub. Para isso funcionar, é preciso gerar um **token de acesso pessoal de granularidade fina**, restrito a este repositório.

### Como gerar o token

1. No GitHub, acesse **Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token**.
2. Em **Repository access**, escolha **"Only select repositories"** e selecione apenas este repositório — nunca marque "All repositories".
3. Em **Permissions → Repository permissions**, defina **"Contents"** como **"Read and write"**. Nenhuma outra permissão é necessária.
4. Defina a **expiração** para o prazo máximo permitido (atualmente 1 ano). O GitHub não permite tokens sem expiração — quando vencer, basta gerar outro em menos de um minuto.
5. Copie o token gerado (ele só aparece uma vez).

### Como usar o token no app

- Clique em **"⚙️ Conectar GitHub"**, no topo da página.
- Preencha usuário/organização, nome do repositório, branch e cole o token.
- **O token não é salvo em nenhum lugar** — nem em cookies, nem em localStorage, nem no código da página. Ele fica só na memória do navegador durante aquela sessão, e desaparece ao fechar a aba. Isso significa que cada pessoa (ou o grupo, se decidirem compartilhar o mesmo token) precisa colá-lo novamente sempre que abrir a página para editar.
- **Por que isso importa:** qualquer código que roda no navegador de uma página pública pode ser inspecionado por quem souber onde olhar ("Ver código-fonte" ou "Inspecionar"). Se o token estivesse embutido no HTML, ficaria exposto publicamente. Colando-o a cada sessão, o token nunca aparece no código nem fica salvo em disco.

### Sobre o risco

Como os dados deste projeto não são sensíveis (não há informação pessoal, sigilosa ou de segurança), o pior cenário realista em caso de uso indevido do token é alguém alterar ou apagar conteúdo do arquivo de indicadores — e isso é **totalmente reversível** pelo histórico de commits do próprio Git (`git revert` ou reverter pela interface do GitHub). Por isso, um único token compartilhado pela equipe, com o cuidado de nunca embuti-lo no código, é uma escolha razoável para este caso de uso.

Se em algum momento o token for exposto ou comprometido, revogue-o imediatamente em **Settings → Developer settings → Personal access tokens** e gere um novo.

---

## Exportação

- **Markdown**: gera um `.md` com toda a estrutura do MAPr e os indicadores já alocados em seus blocos/grupos — pronto para colar num documento ou anexar ao SEI.
- **PDF**: usa a função de impressão do próprio navegador (Ctrl+P / Cmd+P), com uma folha de estilos dedicada que oculta os elementos de interface e mostra só o conteúdo. Ao imprimir, escolha "Salvar como PDF" no destino da impressão.

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

Os cinco blocos (Recursos, Atividades, Produtos, Resultados, Impactos) são um vocabulário fixo embutido no motor — não são editáveis por conteúdo, só a **aplicabilidade** de cada um a cada nível.

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
