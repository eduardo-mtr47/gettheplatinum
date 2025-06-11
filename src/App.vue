<template>
  <div class="min-h-screen bg-black text-white flex flex-col items-center px-4 py-8">
    <h1 class="text-4xl font-bold mb-6">GetThePlatinum</h1>
    <div v-if="!user">
      <form @submit.prevent="login" class="flex flex-col gap-4 w-80">
        <input v-model="email" type="email" placeholder="Email" required class="px-3 py-2 rounded text-black" />
        <input v-model="password" type="password" placeholder="Password" required class="px-3 py-2 rounded text-black" />
        <button class="bg-white text-black px-4 py-2 rounded font-semibold hover:bg-gray-200">Login</button>
      </form>
    </div>
    <div v-else class="w-full max-w-xl">
      <div class="mb-4 flex justify-between items-center">
        <span class="mr-4">Logged in as {{ user }}</span>
        <button @click="logout" class="text-sm text-gray-400 underline">Logout</button>
      </div>

      <div v-if="!activeGame">
        <h2 class="text-2xl mb-4">Your Games</h2>
        <ul class="space-y-4">
          <li v-for="game in games" :key="game.id" class="border p-4 rounded">
            <div class="flex justify-between items-center">
              <span>{{ game.title }}</span>
              <span>{{ progress(game) }}%</span>
            </div>
            <button @click="openGame(game)" class="mt-2 text-sm text-black bg-white px-2 py-1 rounded">Details</button>
          </li>
        </ul>
      </div>

      <div v-else>
        <button @click="activeGame = null" class="text-sm mb-4 underline">Back to list</button>
        <h2 class="text-2xl mb-4">{{ activeGame.title }}</h2>
        <ul class="space-y-2">
          <li v-for="trophy in activeGame.trophies" :key="trophy.id" class="flex items-center">
            <input type="checkbox" v-model="trophy.achieved" @change="saveGames" class="mr-2">
            <span :class="{'line-through text-gray-400': trophy.achieved}">{{ trophy.name }}</span>
          </li>
        </ul>
        <p class="mt-4">Progress: {{ progress(activeGame) }}%</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, watch } from 'vue'
import { mockGames } from './mockData'

const email = ref('')
const password = ref('')
const user = ref(localStorage.getItem('user') || '')
const activeGame = ref(null)
const games = reactive(loadGames())

function loadGames() {
  const saved = localStorage.getItem('games')
  return saved ? JSON.parse(saved) : JSON.parse(JSON.stringify(mockGames))
}

function saveGames() {
  localStorage.setItem('games', JSON.stringify(games))
}

function progress(game) {
  const achieved = game.trophies.filter(t => t.achieved).length
  return Math.round((achieved / game.trophies.length) * 100)
}

function login() {
  user.value = email.value
  localStorage.setItem('user', user.value)
  saveGames()
}

function logout() {
  user.value = ''
  localStorage.removeItem('user')
}

function openGame(game) {
  activeGame.value = game
}

watch(games, saveGames, { deep: true })
</script>
