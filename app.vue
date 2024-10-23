<script setup lang="ts">
const code = ref("");
const output = ref("");
const darkMode = ref(false);
let originalConsoleLog = console.log;

const captureConsole = () => {
  console.log = function (...args) {
    output.value +=
      args
        .map((arg) =>
          typeof arg === "object" ? JSON.stringify(arg, null, 2) : arg
        )
        .join(" ") + "\n";
    originalConsoleLog.apply(console, args);
  };
};

const runCode = () => {
  try {
    output.value = "";
    captureConsole();
    eval(code.value);
  } catch (error: any) {
    output.value = `Error: ${error.message}`;
  }
};

watch(code, runCode);

onMounted(() => {
  return () => {
    console.log = originalConsoleLog;
  };
});
</script>

<template>
  <div :class="{ dark: darkMode }" class="flex h-screen">
    <div class="w-1/2 p-4">
      <h1 class="text-3xl font-bold mb-4">CodeSphereX by Waleed Tariq</h1>
      <MonacoEditor
        lang="javascript"
        class="h-full w-full border"
        v-model="code"
        :theme="darkMode ? 'vs-dark' : 'vs-light'"
      />
    </div>
    <div
      class="w-1/2 p-4 bg-gray-100 dark:bg-gray-900 text-black dark:text-white"
    >
      <label for="darkMode" class="block mb-2">Dark Mode:</label>
      <input type="checkbox" id="darkMode" v-model="darkMode" class="mb-4" />
      <h3 class="text-lg font-semibold">Output:</h3>
      <pre
        class="border p-4 mt-2 h-full w-full bg-white dark:bg-gray-800 overflow-auto"
      >
        {{ output }}
      </pre>
    </div>
  </div>
</template>

<style scoped>
.h-screen {
  height: 100vh;
}
</style>
