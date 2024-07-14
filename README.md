# WXT + Vue 3 + Rust

WXT template + WASM with Rust.

This template should help get you started developing with Vue 3 in WXT.

## Recommended IDE Setup

- [VS Code](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur) + [TypeScript Vue Plugin (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin).

# Instructions starting from scratch

[Install Rust](https://www.rust-lang.org/tools/install)

[Install pnpm](https://pnpm.io/installation) and bootstrap WXT with your favourite frontend framework. I will be using Vue.

```sh
pnpm dlx wxt@latest init <project-name>
```

You can then follow the [guide at mozilla](https://developer.mozilla.org/en-US/docs/WebAssembly/Rust_to_Wasm) or my condensed version

Set up project

```sh
cargo install wasm-pack
cargo new --lib wasm_example
```

Put this code in `src/lib.rs`

```rust
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
extern "C" {
    pub fn alert(s: &str);
}

#[wasm_bindgen]
pub fn greet(name: &str) {
    alert(&format!("Hello, {}!", name));
}

#[wasm_bindgen]
pub fn sum(a: i32, b: i32) -> i32 {
    a + b
}
```

Edit your `Cargo.toml`

```toml
[package]
name = "hello-wasm"
version = "0.1.0"
# authors = ["Your Name <you@example.com>"]
description = "A sample project with wasm-pack"
license = "MIT/Apache-2.0"
repository = "https://github.com/yourgithubusername/hello-wasm"
edition = "2018"

[lib]
crate-type = ["cdylib"]

[dependencies]
wasm-bindgen = "0.2"
```

Build rust into wasm

```sh
wasm-pack build --target web
```

Rust set up is done. While rust is compiling open `components/HelloWorld.vue` and edit it as follows

```vue
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
```

Make sure you have `content_security_policy` set in your wxt config

For Chrome

```ts
import { defineConfig } from "wxt";

// See https://wxt.dev/api/config.html
export default defineConfig({
  modules: ["@wxt-dev/module-vue"],
  manifest: {
    content_security_policy: {
      extension_pages:
        "script-src 'self' 'wasm-unsafe-eval'; object-src 'self'",
    },
  },
});
```

For Firefox it should be

```ts
import { defineConfig } from "wxt";

// See https://wxt.dev/api/config.html
export default defineConfig({
  modules: ["@wxt-dev/module-vue"],
  manifest: {
    content_security_policy: "script-src 'self' 'wasm-eval'; object-src 'self'",
  },
});
```

PNPM install and run your extension


```sh
pnpm install
```

If you're getting a warning about vite, install it.

```sh
pnpm install vite
```

Then

```sh
pnpm dev
```

or

```sh
pnpm dev:firefox
```

Congratulations!🎉 You are now ready to experiment with Rust WASM and web extensions.
