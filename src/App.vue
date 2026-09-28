<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import CanvasElement from "./components/CanvasElement.vue";

const PALETTE = [
  { tipo: "botao", label: "Botão", w: 120, h: 40, props: { label: "Clique" } },
  { tipo: "texto", label: "Texto", w: 200, h: 40, props: { text: "Texto de exemplo" } },
  { tipo: "imagem", label: "Imagem", w: 160, h: 120, props: { url: "https://picsum.photos/seed/drag/160/120" } },
  { tipo: "input", label: "Input", w: 200, h: 40, props: { placeholder: "Digite..." } },
  { tipo: "card", label: "Card", w: 200, h: 120, props: { title: "Título", body: "Conteúdo do card." } },
];

const elements = ref([]);
const selectedId = ref(null);
const canvas = ref(null);
let nextId = 1;
let dragType = null;

function onDragStart(e, item) {
  dragType = item;
  e.dataTransfer.effectAllowed = "copy";
}

function onDragOver(e) {
  e.preventDefault();
  e.dataTransfer.dropEffect = "copy";
  canvas.value.classList.add("dropzone");
}

function onDragLeave() {
  canvas.value.classList.remove("dropzone");
}

function onDrop(e) {
  e.preventDefault();
  canvas.value.classList.remove("dropzone");
  if (!dragType) return;
  const rect = canvas.value.getBoundingClientRect();
  const x = Math.round((e.clientX - rect.left) / 8) * 8;
  const y = Math.round((e.clientY - rect.top) / 8) * 8;
  elements.value.push({
    id: nextId++,
    tipo: dragType.tipo,
    x,
    y,
    w: dragType.w,
    h: dragType.h,
    props: { ...dragType.props },
  });
  dragType = null;
}

function selectElement(id) {
  selectedId.value = id;
}

function deselect() {
  selectedId.value = null;
}

function moveElement(id, x, y) {
  const el = elements.value.find((e) => e.id === id);
  if (el) { el.x = x; el.y = y; }
}

function resizeElement(id, w, h) {
  const el = elements.value.find((e) => e.id === id);
  if (el) { el.w = w; el.h = h; }
}

function editElement(id, patch) {
  const el = elements.value.find((e) => e.id === id);
  if (el) el.props = { ...el.props, ...patch };
}

function deleteElement(id) {
  elements.value = elements.value.filter((e) => e.id !== id);
  if (selectedId.value === id) selectedId.value = null;
}

function onKey(e) {
  if ((e.key === "Delete" || e.key === "Backspace") && selectedId.value !== null) {
    const active = document.activeElement;
    if (active && (active.tagName === "INPUT" || active.isContentEditable)) return;
    deleteElement(selectedId.value);
  }
  if (e.key === "Escape") deselect();
}

let confirmClear = false;
function clearAll() {
  if (!confirmClear) {
    confirmClear = true;
    setTimeout(() => { confirmClear = false; }, 2500);
    return;
  }
  elements.value = [];
  selectedId.value = null;
  confirmClear = false;
}

onMounted(() => window.addEventListener("keydown", onKey));
onBeforeUnmount(() => window.removeEventListener("keydown", onKey));
</script>

<template>
  <main class="app">
    <h1>Drag to Create Layout</h1>
    <div class="layout">
      <aside class="palette">
        <h2>Componentes</h2>
        <div
          v-for="item in PALETTE"
          :key="item.tipo"
          class="palette-item"
          draggable="true"
          @dragstart="onDragStart($event, item)"
        >{{ item.label }}</div>
        <button class="clear-btn" @click="clearAll">{{ confirmClear ? "Confirmar?" : "Limpar tudo" }}</button>
      </aside>
      <div
        ref="canvas"
        class="canvas"
        @dragover="onDragOver"
        @dragleave="onDragLeave"
        @drop="onDrop"
        @pointerdown="deselect"
      >
        <CanvasElement
          v-for="el in elements"
          :key="el.id"
          :element="el"
          :selected="el.id === selectedId"
          @select="selectElement(el.id)"
          @move="(x, y) => moveElement(el.id, x, y)"
          @resize="(w, h) => resizeElement(el.id, w, h)"
          @edit="(p) => editElement(el.id, p)"
          @delete="deleteElement(el.id)"
        />
        <p v-if="elements.length === 0" class="empty">Arraste componentes da paleta →</p>
      </div>
    </div>
  </main>
</template>
