<script setup>
import { ref } from "vue";

const props = defineProps({
  element: Object,
  selected: Boolean,
});

const emit = defineEmits(["select", "move", "resize", "edit", "delete"]);

const editing = ref(false);
const GRID = 8;

function onPointerDown(e) {
  e.stopPropagation();
  emit("select");
  if (editing.value) return;
  const startX = e.clientX;
  const startY = e.clientY;
  const origX = props.element.x;
  const origY = props.element.y;
  let pointerId = e.pointerId;
  e.target.setPointerCapture(e.pointerId);

  function onMove(ev) {
    if (ev.pointerId !== pointerId) return;
    const nx = Math.round((origX + ev.clientX - startX) / GRID) * GRID;
    const ny = Math.round((origY + ev.clientY - startY) / GRID) * GRID;
    emit("move", nx, ny);
  }
  function onUp(ev) {
    if (ev.pointerId !== pointerId) return;
    window.removeEventListener("pointermove", onMove);
    window.removeEventListener("pointerup", onUp);
  }
  window.addEventListener("pointermove", onMove);
  window.addEventListener("pointerup", onUp);
}

function onResizeDown(e) {
  e.stopPropagation();
  e.preventDefault();
  const startX = e.clientX;
  const startY = e.clientY;
  const origW = props.element.w;
  const origH = props.element.h;
  let pointerId = e.pointerId;

  function onMove(ev) {
    if (ev.pointerId !== pointerId) return;
    const nw = Math.max(48, Math.round((origW + ev.clientX - startX) / GRID) * GRID);
    const nh = Math.max(32, Math.round((origH + ev.clientY - startY) / GRID) * GRID);
    emit("resize", nw, nh);
  }
  function onUp(ev) {
    if (ev.pointerId !== pointerId) return;
    window.removeEventListener("pointermove", onMove);
    window.removeEventListener("pointerup", onUp);
  }
  window.addEventListener("pointermove", onMove);
  window.addEventListener("pointerup", onUp);
}

function onDoubleClick(e) {
  if (props.element.tipo === "imagem") {
    const url = prompt("URL da imagem:", props.element.props.url);
    if (url) emit("edit", { url });
    return;
  }
  editing.value = true;
}

function onBlur() {
  editing.value = false;
}
</script>

<template>
  <div
    class="canvas-element"
    :class="{ selected }"
    :style="{ transform: `translate(${element.x}px, ${element.y}px)`, width: element.w + 'px', height: element.h + 'px' }"
    @pointerdown="onPointerDown"
    @dblclick="onDoubleClick"
  >
    <button v-if="selected" class="del" @click.stop="$emit('delete')">✕</button>
    <div v-if="selected" class="handle" @pointerdown="onResizeDown"></div>

    <button v-if="element.tipo === 'botao'" class="el-btn" :disabled="editing">{{ element.props.label }}</button>

    <p
      v-else-if="element.tipo === 'texto'"
      class="el-text"
      :contenteditable="editing"
      @blur="onBlur"
    >{{ element.props.text }}</p>

    <img
      v-else-if="element.tipo === 'imagem'"
      class="el-img"
      :src="element.props.url"
      alt=""
      draggable="false"
    />

    <input
      v-else-if="element.tipo === 'input'"
      class="el-input"
      :aria-label="element.props.placeholder || 'Campo de entrada'"
      :placeholder="element.props.placeholder"
      :disabled="!editing"
    />

    <div v-else-if="element.tipo === 'card'" class="el-card">
      <h4>{{ element.props.title }}</h4>
      <p>{{ element.props.body }}</p>
    </div>
  </div>
</template>
