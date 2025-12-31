<script setup>
import { computed } from 'vue'

const BASE_URL = window.location.origin

const modules = import.meta.glob('/src/assets/**/*', {
  eager: true,
  as: 'url'
})

const TYPES = {
  image: ['png', 'jpg', 'jpeg', 'gif', 'svg', 'webp'],
  video: ['mp4', 'webm', 'ogg'],
  audio: ['mp3', 'wav', 'm4a']
}

function getType(name) {
  const ext = name.split('.').pop().toLowerCase()
  if (TYPES.image.includes(ext)) return 'image'
  if (TYPES.video.includes(ext)) return 'video'
  if (TYPES.audio.includes(ext)) return 'audio'
  return null
}

const resources = computed(() => {
  const res = { image: [], video: [], audio: [] }

  Object.entries(modules).forEach(([path, url]) => {
    const name = path.replace('/src/assets/', '')
    const type = getType(name)
    if (!type) return

    res[type].push({
      name,
      url,
      fullUrl: BASE_URL + url
    })
  })

  return res
})

async function copyLink(url) {
  await navigator.clipboard.writeText(url)
  alert('已复制资源链接')
}
</script>

<template>
  <div class="min-h-screen bg-zinc-50 dark:bg-zinc-900
           text-zinc-900 dark:text-zinc-100 p-6">
    <h1 class="mb-8 text-2xl font-bold">📦 Assets 资源浏览</h1>

    <!-- 图片 -->
    <section v-if="resources.image.length" class="mb-10">
      <h2 class="mb-4 text-xl font-semibold">🖼 图片</h2>
      <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-6 gap-4">
        <div v-for="item in resources.image" :key="item.name" @click="copyLink(item.fullUrl)" class="cursor-pointer rounded-xl border border-zinc-200 dark:border-zinc-700
                 bg-white dark:bg-zinc-800 p-3 transition hover:shadow-lg">
          <img :src="item.url" class="h-36 w-full object-contain rounded-md bg-zinc-100 dark:bg-zinc-700" />
          <div class="mt-2 truncate text-xs text-zinc-600 dark:text-zinc-400">
            {{ item.name }}
          </div>
        </div>
      </div>
    </section>

    <!-- 视频 -->
    <section v-if="resources.video.length" class="mb-10">
      <h2 class="mb-4 text-xl font-semibold">🎬 视频</h2>
      <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4">
        <div v-for="item in resources.video" :key="item.name" @click="copyLink(item.fullUrl)" class="cursor-pointer rounded-xl border border-zinc-200 dark:border-zinc-700
                 bg-white dark:bg-zinc-800 p-3 transition hover:shadow-lg">
          <video :src="item.url" controls class="h-36 w-full object-contain rounded-md bg-black" />
          <div class="mt-2 truncate text-xs text-zinc-600 dark:text-zinc-400">
            {{ item.name }}
          </div>
        </div>
      </div>
    </section>

    <!-- 音乐 -->
    <section v-if="resources.audio.length">
      <h2 class="mb-4 text-xl font-semibold">🎵 音乐</h2>
      <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
        <div v-for="item in resources.audio" :key="item.name" @click="copyLink(item.fullUrl)" class="cursor-pointer rounded-xl border border-zinc-200 dark:border-zinc-700
                 bg-white dark:bg-zinc-800 p-3 transition hover:shadow-lg">
          <div class="flex h-36 flex-col justify-center gap-2">
            <div class="truncate text-sm font-medium">
              🎧 {{ item.name }}
            </div>
            <audio :src="item.url" controls class="w-full" />
          </div>
        </div>
      </div>
    </section>
  </div>
</template>
