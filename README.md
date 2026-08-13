# QA Delivery Roadmap

Roadmap de entregas da QA, com visualização em Gantt. Editável direto pela página — sem tocar em código, sem planilha manual no Figma, sem arquivo local. Os dados ficam versionados neste repositório (`data.json`) e a página é publicada via GitHub Pages.

## O que este projeto substitui

Antes, o roadmap era montado manualmente no Figma: cada entrega era uma caixa clonada à mão, com texto, datas, link do Teams e avatar digitados dentro. Aqui, cada entrega é um registro em `data.json`; a página (`index.html`) lê esses registros e desenha a timeline automaticamente — posição, largura e cor da barra são calculadas a partir da data de início/fim e da categoria.

## Estrutura do repositório

| Arquivo | Função |
|---|---|
| `index.html` | A página do roadmap (timeline + painel de edição). Não precisa editar para uso normal. |
| `data.json` | Categorias e entregas — a fonte de dados real. Pode ser editada pela página ou direto no GitHub. |
| `tutorial.html` | Tutorial ilustrado, passo a passo, para gerar o token do GitHub usado ao salvar. |

## Como publicar (uma vez)

1. Repositório precisa ser **público** (GitHub Pages grátis exige isso, a menos que sua organização tenha plano com Pages privado).
2. `index.html` e `data.json` devem estar na **raiz** do repositório.
3. **Settings → Pages** → Source: **Deploy from a branch** → branch `main`, pasta `/ (root)` → **Save**.
4. Aguarde ~1 minuto. O site fica em `https://SEU_USUARIO.github.io/NOME_DO_REPOSITORIO/`.

A página detecta automaticamente o usuário/repositório pela própria URL — não é necessário editar nada no código para isso funcionar.

## Como visualizar

Basta abrir o link do GitHub Pages. O roadmap carrega os dados mais recentes de `data.json` automaticamente, para qualquer pessoa com o link — sem login e sem instalar nada.

## Como editar

1. Abra o link do GitHub Pages. No topo da página já tem um botão **🔄 Atualizar** para buscar a versão mais recente sem precisar abrir o painel de edição.
2. Clique **✏️ Editar roadmap**. O painel tem três abas: **Entregas**, **Categorias** e **Sincronização**. As abas Entregas e Categorias já têm o botão **💾 Salvar no GitHub** no topo — não é preciso ir até a aba Sincronização só para salvar.

### Aba Entregas
- **+ Nova entrega** para adicionar, ou ✏️/🗑️ em um item da lista para editar/excluir.
- **Data de fim é opcional.** Se você preencher só a data de início e deixar o fim em branco, a página completa automaticamente com **+1 semana** a partir do início (o campo já vem preenchido sozinho quando você sai do campo de início, mas pode editar livremente).

### Aba Categorias
- **+ Nova categoria**: escolha nome, cor de fundo e cor do texto (com pré-visualização em tempo real). A categoria passa a existir tanto na legenda quanto na lista de opções ao criar/editar uma entrega. O id interno é **numérico e sequencial** (1, 2, 3...) e nunca muda — mesmo que o nome seja alterado depois.
- ✏️ em uma categoria existente: renomear ou trocar as cores. Renomear é seguro — como o id não depende do nome, nenhuma entrega perde a categoria por causa de uma renomeação.
- 🗑️ em uma categoria: se nenhuma entrega usa essa categoria, ela é removida direto. Se alguma entrega usa, a página pede para escolher outra categoria de destino antes de excluir — as entregas afetadas são movidas automaticamente, nada fica "solto".
- Não é possível excluir a última categoria restante (é preciso ter pelo menos uma).

### Aba Sincronização
1. Clique **💾 Salvar no GitHub**. Na primeira vez, vai pedir um token — veja o tutorial ilustrado em [`tutorial.html`](tutorial.html) (passo a passo com prints) ou o resumo abaixo:
   - GitHub → **Settings → Developer settings → Fine-grained tokens → Generate new token**
   - **Expiration**: defina até o **final de dezembro** — o token tem validade máxima de 366 dias, então marque um lembrete para renovar antes de vencer.
   - Repository access: **Only select repositories** → escolha este repositório
   - Permissions → **Contents: Read and write**
   - Gere e cole o token na página. Ele fica salvo só no seu navegador, nunca é commitado.
2. Depois de conectado, **Salvar no GitHub** grava entregas e categorias juntas (um commit real) e **🔄 Atualizar** busca a versão mais recente.

Cada pessoa que for editar conecta o próprio token, uma vez, no próprio navegador. Quem só visualiza não precisa de nada.

## Conflitos de edição simultânea

Se duas pessoas salvarem quase ao mesmo tempo, a segunda tentativa é rejeitada pela API do GitHub (em vez de sobrescrever silenciosamente). A página avisa para clicar em **Atualizar**, refazer a mudança e salvar novamente.

## Formato de `data.json`

```json
{
  "categories": [
    { "id": "1", "name": "Cyber Threat Intelligence", "bg": "#BFE0F7", "fg": "#1B4965" }
  ],
  "deliveries": [
    {
      "id": "d1",
      "name": "Nome da entrega",
      "categoryId": "1",
      "categoryName": "Cyber Threat Intelligence",
      "start": "2025-11-10",
      "end": "2025-11-21",
      "link": "https://teams.microsoft.com/...",
      "status": ["published"],
      "owners": ["MC"]
    }
  ]
}
```

- `id` da categoria é **numérico e permanente** — nunca é recalculado a partir do nome, então renomear uma categoria pela aba "Categorias" nunca desconecta uma entrega da sua categoria.
- `categoryId` (na entrega) é a referência real, usada para calcular cor e agrupamento. `categoryName` é apenas uma cópia legível do nome, recalculada automaticamente sempre que a categoria é salva — não precisa (e não deve) ser editada manualmente separada do `categoryId`.
- `end` é opcional: se ausente ou vazio, a página assume `start + 7 dias` ao salvar.
- `status`: qualquer combinação de `"published"` (👍 Publicação em #update-deliveries) e `"homolog"` (🌗 Homologação fora do lifecycle).
- `owners`: iniciais do(s) responsável(is) pela QA; cada conjunto de iniciais recebe uma cor automática, consistente em todas as barras.

O arquivo é sempre salvo com as entregas ordenadas por data de início — isso mantém o histórico de commits legível e reduz conflitos de merge. Formatos antigos (categoria por nome/slug, sem `categoryId`/`categoryName`) ainda são lidos normalmente — a página migra sozinha para ids numéricos na primeira vez que carregar.

## Backup manual

O botão **⬇ Baixar backup local (.json)** no painel de edição salva uma cópia de segurança do estado atual, independente do GitHub.

## Limitações conhecidas

- Sem autenticação de usuários — qualquer pessoa com um token de escrita no repositório pode editar. Adequado para um time interno de confiança; não é um controle de acesso granular.
- Sem histórico de "quem editou o quê" além do que o próprio Git já registra nos commits.
- O site publicado pode levar até ~1 minuto para refletir um novo commit.
