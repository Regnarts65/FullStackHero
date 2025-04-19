<script setup>
import { ref, onMounted } from 'vue'
import PocketBase from 'pocketbase'

const pb = new PocketBase("http://127.0.0.1:8090")
const records = ref([])
const todoInput = ref("")
const hi = "My name is:";
const isTrue = ref(true);
const prayTimeLink =
  "https://www.e-solat.gov.my/index.php?r=esolatApi/takwimsolat&period=today&zone=PLS01";
const name = ref("Vichaviv Gorgon");
const number = ref(0);
const games = ref(["Crisis Core", "Monster Hunter: World", "Dota 2", "Elden Ring", "Cytus"]);
const userInput = ref("");
const prayerTime = ref({});

// Functions
const increment = () => {
  number.value++;
};

const startInterval = () => {
  setInterval(increment, 1000);
};

const addToList = () => {
  if (userInput.value.trim()) {
    games.value.push(userInput.value.trim());
    userInput.value = ""; // Clear input after adding
  }
};

const getPrayerTime = async () => {
  try {
    const response = await fetch(prayTimeLink);
    const data = await response.json();
    prayerTime.value = data.prayerTime[0];
    console.log(prayerTime.value);
  } catch (error) {
    console.error("Error fetching prayer times:", error);
  }
};

function createTodo(){
  const newTodo = {
  item: todoInput.value,
  isDone: false
  }

  pb.collection('todos').create(newTodo).then(res => {
    records.value.push(res)
    todoInput.value = ""
  })
}

// Fetch prayer times on component mount
onMounted(() => {
  getPrayerTime();
  pb.collection('todos').getFullList({
}).then (data => records.value = data)
});
</script>

<template>
  <div class="container mx-auto p-4 bg-gray-50 min-h-screen">
    <!-- Header Section -->
    <header class="text-center mb-6">
      <h1 class="text-4xl font-bold text-gray-800">{{ number }}</h1>
      <button @click="startInterval"
        class="mt-4 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 transition duration-300">
        Increment
      </button>
    </header>

    <!-- Input Section -->
    <section class="mb-6">
      <input type="text" v-model="todoInput" @keyup.enter="createTodo" placeholder="Create new item in to do list"
      class="w-full p-2 border border-gray-300 rounded focus:outline-none focus:ring-2 focus:ring-red-500" />
      <input type="text" v-model="name" placeholder="Enter your name"
        class="w-full p-2 border border-gray-300 rounded focus:outline-none focus:ring-2 focus:ring-blue-500" />
      <input type="text" v-model="userInput" @keyup.enter="addToList" placeholder="Add a game"
        class="mt-2 w-full p-2 border border-gray-300 rounded focus:outline-none focus:ring-2 focus:ring-green-500" />
    </section>

    <!-- Display Name -->
    <h1 class="text-2xl font-semibold text-gray-700">{{ hi }}</h1>
    <h2 class="text-xl text-gray-600">{{ name }}</h2>

    <!-- Game List -->
    <ol class="mt-4 list-decimal pl-4">
      <li v-for="(game, index) in games" :key="index" class="text-gray-800">
        {{ game }}
      </li>
    </ol>

    <!-- Toggle Button -->
    <button @click="isTrue = !isTrue"
      class="mt-4 px-4 py-2 bg-purple-500 text-white rounded hover:bg-purple-600 transition duration-300">
      {{ isTrue ? "Monster Hunter is the GOAT" : "Click me!" }}
    </button>

    <!-- Prayer Times Button -->
    <button @click="getPrayerTime"
      class="mt-4 px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600 transition duration-300">
      Get Prayer Times
    </button>

    <!-- Prayer Times Table -->
    <div v-if="Object.keys(prayerTime).length" class="mt-6 border rounded-lg bg-white shadow-md p-4">
      <table class="w-full text-sm text-left text-gray-500">
        <thead class="text-xs text-gray-700 uppercase bg-gray-50">
          <tr>
            <th class="px-6 py-3">Imsak</th>
            <th class="px-6 py-3">Fajr</th>
            <th class="px-6 py-3">Syuruk</th>
            <th class="px-6 py-3">Dhuha</th>
            <th class="px-6 py-3">Dhuhr</th>
            <th class="px-6 py-3">Asr</th>
            <th class="px-6 py-3">Maghrib</th>
            <th class="px-6 py-3">Isya'</th>
          </tr>
        </thead>
        <tbody>
          <tr class="bg-white border-b">
            <td class="px-6 py-4">{{ prayerTime.imsak }}</td>
            <td class="px-6 py-4">{{ prayerTime.fajr }}</td>
            <td class="px-6 py-4">{{ prayerTime.syuruk }}</td>
            <td class="px-6 py-4">{{ prayerTime.dhuha }}</td>
            <td class="px-6 py-4">{{ prayerTime.dhuhr }}</td>
            <td class="px-6 py-4">{{ prayerTime.asr }}</td>
            <td class="px-6 py-4">{{ prayerTime.maghrib }}</td>
            <td class="px-6 py-4">{{ prayerTime.isha }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 1200px;
}

input:focus {
  outline: none;
}
</style>