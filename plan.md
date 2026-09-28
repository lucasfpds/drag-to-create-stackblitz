# Drag to Create Layout

> Área onde o usuário arrasta e solta componentes para montar um layout livre estilo construtor de páginas.

## Stack

- Vite + Vue 3 (`<script setup>`, JavaScript) — sem TypeScript, sem lint/test, zero libs extras
- CSS puro em `src/styles.css`
- Arquivos: `index.html`, `package.json` (deps: `vue`), `vite.config.js`, `.stackblitzrc`, `src/main.js`, `src/App.vue`, `src/components/CanvasElement.vue`, `src/styles.css`

## Implementação

### 1. Paleta de componentes

- Sidebar com 5 tipos: botão, texto, imagem, input, card — itens com `draggable="true"` (HTML5 Drag & Drop nativo) e miniatura
- Canvas à direita: fundo quadriculado (grade 8px via CSS `background-image` com gradientes), highlight de dropzone no `dragover`

### 2. Drop e posicionamento livre

- No `drop`: cria `{ id, tipo, x, y, w, h, props }` na posição do ponteiro, com **snap à grade** (`Math.round(x / 8) * 8`)
- Elementos renderizados em `position: absolute` + `transform: translate(x, y)`, cada tipo com estilo próprio (imagem usa picsum como default)
- `components/CanvasElement.vue`: renderiza um elemento conforme o tipo + handles de seleção/resize

### 3. Manipulação

- Seleção por clique (outline + handles); clique no vazio desseleciona; Esc também
- Mover: arrastar o elemento selecionado (pointer events + snap à grade)
- Redimensionar: handle no canto inferior-direito (mín. 48×32)
- Editar: duplo clique no texto/botão/input → `contenteditable` inline (mesmo cuidado do WYSIWYG: não re-renderizar enquanto edita); imagem → trocar URL
- Deletar: Delete/Backspace ou ✕ do selecionado; "limpar tudo" em 2 passos (clicar → confirmar)

## Checklist — 100% da descrição

- [ ] Arrastar componentes da paleta e soltar no canvas
- [ ] Posicionamento livre com snap à grade
- [ ] Selecionar, mover, redimensionar
- [ ] Editar conteúdo e deletar
- [ ] Montar layout livre (estilo page builder)
