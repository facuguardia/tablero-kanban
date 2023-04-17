<template>
  <!-- Logo -->
  <div
    class="w-full h-screen overflow-scroll flex flex-col justify-between bg-gray-200"
  >
    <nav class="flex justify-between items-center px-10 pt-5">
      <div>
        <h1 class="text-2xl font-bold">Tablero Kanban</h1>
      </div>
    </nav>

    <!-- Mensaje de no hay tableros -->
    <div
      v-if="boards.length === 0"
      class="flex justify-center items-center h-full rounded-md"
    >
      <div class="flex flex-col items-center">
        <p class="text-3xl font-bold mb-4">No hay tableros</p>
        <p class="text-lg mb-4">
          ¡Crea un nuevo tablero para comenzar a organizar tus tareas!
        </p>
        <button
          @click.prevent="handleNewBoard"
          class="px-4 py-2 bg-slate-50 hover:bg-green-500 text-green-500 hover:text-slate-50 rounded-md font-semibold"
        >
          Crear Tablero
        </button>
      </div>
    </div>

    <!-- Tableros -->
    <div>
      <div class="flex gap-10 p-8">
        <div
          class="flex flex-col items-center gap-5 w-72 h-96 p-5 rounded-md shadow-lg bg-slate-50"
          @drop="onDrop($event, board)"
          @dragover.prevent
          @dragenter.prevent
          v-for="board in boards"
          :key="board.id"
        >
          <!-- Titulo Tablero -->
          <div class="flex justify-between items-center w-full">
            <div class="text-2xl font-semibold">
              {{ board.name }}
            </div>
            <!-- Boton para eliminar tablero -->
            <div @click="handleDeleteBoard(board)">
              <font-awesome-icon
                class="text-slate-300 hover:bg-red-500 hover:text-slate-50 transition-all duration-300 text-md font-bold rounded-full p-2 cursor-pointer"
                :icon="faTimes"
              />
            </div>
          </div>
          <!-- Input para agregar nueva tarea -->
          <InputNew @on-new-item="(text) => handleNewItem(text, board)" />
          <!-- Tareas -->
          <div
            class="text-md bg-slate-200 flex justify-between py-2 px-5 w-52 rounded-lg"
            draggable="true"
            @dragstart="startDrag($event, board, item)"
            v-for="item in board.items"
            :key="item.id"
          >
            {{ item.title }}
            <!-- Boton para eliminar tarea -->
            <button @click="handleDeleteItem(board, item)">
              <font-awesome-icon class="text-red-400" :icon="faTrashAlt" />
            </button>
          </div>
        </div>
      </div>
    </div>
    <!-- Agregar tablero -->
    <div
      class="flex justify-end items-center pr-5 pb-5"
      v-if="boards.length > 0"
    >
      <button
        class="bg-green-500 hover:bg-green-600 text-white font-bold text-xl rounded-full py-2 px-3 shadow-md transition-all duration-300"
        @click.prevent="handleNewBoard"
      >
        <font-awesome-icon :icon="faPlus" />
      </button>
    </div>
    <!-- Footer -->
    <footer>
      <p class="text-center text-gray-500 text-xs pb-2">
        &copy;2023 Facu Guardia for QuieroLaCarta.com
      </p>
    </footer>
  </div>
</template>

<script setup>
import { reactive } from "vue";
import InputNew from "./InputNew.vue";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import { faTrashAlt } from "@fortawesome/free-solid-svg-icons";
import { faTimes } from "@fortawesome/free-solid-svg-icons";
import { faPlus } from "@fortawesome/free-solid-svg-icons";

let boards = reactive([]);

const handleNewItem = (text, board) => {
  board.items.push({
    id: crypto.randomUUID(),
    title: text.value,
  });
};

const handleNewBoard = () => {
  const name = prompt("Ingrese el nombre del tablero");
  if (!!name) {
    boards.push({
      id: crypto.randomUUID(),
      name: name,
      items: [],
    });
  }
};

const startDrag = (e, board, item) => {
  e.dataTransfer.setData(
    "text/plain",
    JSON.stringify({ boardId: board.id, itemId: item.id })
  );
};

const onDrop = (e, dest) => {
  const { boardId, itemId } = JSON.parse(e.dataTransfer.getData("text/plain"));
  const originBoard = boards.find((item) => item.id === boardId);
  const originItem = originBoard.items.find((item) => item.id === itemId);

  dest.items.push({ ...originItem });
  originBoard.items = originBoard.items.filter((item) => item.id !== itemId);
};

const handleDeleteItem = (board, item) => {
  board.items = board.items.filter((i) => i.id !== item.id);
};

const handleDeleteBoard = (board) => {
  const index = boards.indexOf(board);
  if (index !== -1) {
    boards.splice(index, 1);
  }
};
</script>
