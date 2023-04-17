<template>
  <form  @submit.prevent="handleSubmit">
    <input class="rounded-full border border-gray-300 px-4 py-2 w-60 text-gray-700 placeholder-gray-400 focus:outline-none focus:border-blue-500" type="text" placeholder="Agrega una tarea" v-model="text" />

  </form>
</template>

<script setup>
import { ref, defineEmits } from "vue";

const text = ref("");
const emits = defineEmits(["onNewItem"]);

const handleSubmit = () => {
  if (text !== "") {
    emits("onNewItem", text);
    text.value = "";
  }
};
</script>
