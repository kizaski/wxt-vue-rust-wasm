<script lang="ts" setup>
import { ref } from "vue";
import init, { sum, greet } from "../wasm_example/pkg/hello_wasm";

defineProps({
  msg: String,
});

const wasmResult = ref<number>(0);

const a = ref(2);
const b = ref(5);

const sumWasm = () => {
  const result = sum(a.value, b.value);
  wasmResult.value = result;
};

const greetWasm = () => {
  greet("WASM");
};

onMounted(async () => {
  await init();
});
</script>

<template>
  <h1>{{ msg }}</h1>

  <div class="card">
    <div>click below to sum {{ a }} and {{ b }}</div>
    <button type="button" @click="sumWasm">result is {{ wasmResult }}</button>
    <hr />
    <button type="button" @click="greetWasm">click me</button>
    <p>
      Edit
      <code>components/HelloWorld.vue</code> to test HMR
    </p>
  </div>

  <p>
    Install
    <a href="https://github.com/vuejs/language-tools" target="_blank">Volar</a>
    in your IDE for a better DX
  </p>
  <p class="read-the-docs">Click on the WXT and Vue logos to learn more</p>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
