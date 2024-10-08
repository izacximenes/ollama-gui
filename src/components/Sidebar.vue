<script setup lang="ts">
import { ref } from 'vue'
import {
  IconMoon,
  IconPlus,
  IconSettings2,
  IconSun,
  IconTrashX,
  IconUserCircle,
  IconMessageCode,
} from '@tabler/icons-vue'

import {
  isDarkMode,
  isSystemPromptOpen,
  toggleSettingsPanel,
  toggleSystemPromptPanel,
} from '../services/appConfig.ts'
import { useChats } from '../services/chat.ts'
import Confirm from './Dialogs/Confirm.vue';

const { sortedChats, activeChat, switchChat, deleteChat, startNewChat, wipeDatabase } =
  useChats()

const showConfirmDeleteAllChats = ref(false)

const onNewChat = () => {
  checkSystemPromptPanel()
  return startNewChat('New chat')
}

const onSwitchChat = (chatId: number) => {
  checkSystemPromptPanel()
  return switchChat(chatId)
}

const checkSystemPromptPanel = () => {
  isSystemPromptOpen.value = false
}

const onConfirmDeleteAllChats = () => {
  wipeDatabase()
  showConfirmDeleteAllChats.value = false
}
</script>

<template>
  <Confirm 
    v-model="showConfirmDeleteAllChats"
    title="Delete all chats" 
    message="Are you sure you want to proceed with this action? This cannot be undone."
    @confirm="onConfirmDeleteAllChats"
    @cancel="showConfirmDeleteAllChats = false"
  />
  <aside class="flex">
    <div
      class="flex h-[100svh] w-60 flex-col overflow-y-auto border-r border-zinc-200 bg-zinc-50 pt-2 sm:h-[100vh] sm:w-64 dark:border-zinc-700 dark:bg-zinc-900"
    >
      <div class="mx-2">
        <button
          @click="onNewChat"
          class="flex w-full gap-x-4 rounded-lg border border-zinc-300 p-4 text-left text-sm font-medium text-zinc-700 transition-colors duration-200 hover:bg-zinc-200 focus:outline-none focus:ring-2 focus:ring-blue-500 dark:border-zinc-700 dark:bg-zinc-700 dark:text-zinc-200 dark:placeholder-zinc-400 dark:hover:bg-zinc-800 dark:focus:ring-blue-500"
        >
          <IconPlus class="h-5 w-5" />
          New Chat
        </button>
      </div>

      <div
        class="h-full space-y-4 overflow-y-auto border-b border-zinc-300 px-2 py-4 dark:border-zinc-700"
      >
        <button
          v-for="chat in sortedChats"
          @click="onSwitchChat(chat.id!)"
          @keyup.delete="deleteChat(chat.id!)"
          :class="{
            'bg-zinc-200 dark:bg-zinc-800': activeChat?.id == chat.id,
          }"
          class="flex w-full flex-col gap-y-1 rounded-lg px-3 py-2 text-left transition-colors duration-200 hover:bg-zinc-200 focus:outline-none focus:ring-2 focus:ring-blue-500 dark:bg-zinc-700 dark:text-zinc-200 dark:placeholder-zinc-400 dark:hover:bg-zinc-800 dark:focus:ring-blue-500"
        >
          <span class="text-sm font-medium leading-none text-zinc-700 dark:text-zinc-200">
            {{ chat.name }}
          </span>
          <span class="text-xs leading-none text-zinc-500 dark:text-zinc-400">
            {{ chat.model }}
          </span>
          <span class="text-xs leading-none text-zinc-500 dark:text-zinc-400">
            {{
              chat.createdAt.toLocaleDateString('no', {
                day: '2-digit',
                month: 'short',
                weekday: 'long',
                hour: '2-digit',
                minute: '2-digit',
                second: '2-digit',
              })
            }}
          </span>
        </button>
      </div>

      <div class="mt-auto w-full space-y-4 px-2 py-4">
        <button
          @click="isDarkMode = !isDarkMode"
          class="flex w-full gap-x-2 rounded-lg px-3 py-2 text-left text-sm font-medium text-zinc-700 transition-colors duration-200 hover:bg-zinc-200 focus:outline-none focus:ring-2 focus:ring-blue-500 dark:bg-zinc-700 dark:text-zinc-200 dark:placeholder-zinc-400 dark:hover:bg-zinc-800 dark:focus:ring-blue-500"
        >
          <IconSun v-if="isDarkMode" class="h-6 w-6" />
          <IconMoon v-else class="h-6 w-6" />

          Toggle dark mode
        </button>
        <button
          @click="showConfirmDeleteAllChats = true"
          class="flex w-full gap-x-2 rounded-lg px-3 py-2 text-left text-sm font-medium text-zinc-700 transition-colors duration-200 hover:bg-zinc-200 focus:outline-none focus:ring-2 focus:ring-blue-500 dark:bg-zinc-700 dark:text-zinc-200 dark:placeholder-zinc-400 dark:hover:bg-zinc-800 dark:focus:ring-blue-500"
        >
          <IconTrashX class="h-6 w-6" />

          Delete chats
        </button>
        <button
          v-if="false"
          class="flex w-full gap-x-2 rounded-lg px-3 py-2 text-left text-sm font-medium text-zinc-700 transition-colors duration-200 hover:bg-zinc-200 focus:outline-none focus:ring-2 focus:ring-blue-500 dark:bg-zinc-700 dark:text-zinc-200 dark:placeholder-zinc-400 dark:hover:bg-zinc-800 dark:focus:ring-blue-500"
        >
          <IconUserCircle class="h-6 w-6" />
          User
        </button>
        <button
          @click="toggleSystemPromptPanel"
          class="flex w-full gap-x-2 rounded-lg px-3 py-2 text-left text-sm font-medium text-zinc-700 transition-colors duration-200 hover:bg-zinc-200 focus:outline-none focus:ring-2 focus:ring-blue-500 dark:bg-zinc-700 dark:text-zinc-200 dark:placeholder-zinc-400 dark:hover:bg-zinc-800 dark:focus:ring-blue-500"
        >
          <IconMessageCode class="h-6 w-6" />

          System prompt
        </button>
        <button
          @click="toggleSettingsPanel"
          class="flex w-full gap-x-2 rounded-lg px-3 py-2 text-left text-sm font-medium text-zinc-700 transition-colors duration-200 hover:bg-zinc-200 focus:outline-none focus:ring-2 focus:ring-blue-500 dark:bg-zinc-700 dark:text-zinc-200 dark:placeholder-zinc-400 dark:hover:bg-zinc-800 dark:focus:ring-blue-500"
        >
          <IconSettings2 class="h-6 w-6" />

          Settings
        </button>
      </div>
    </div>
  </aside>
</template>
