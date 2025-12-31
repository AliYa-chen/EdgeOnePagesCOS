<script setup>
defineProps({
    node: {
        type: Object,
        required: true
    }
})

const emit = defineEmits(['copy'])

function copy(url) {
    emit('copy', url)
}
</script>

<template>
    <div>
        <template v-for="(value, key) in node" :key="key">
            <!-- 文件卡片 -->
            <div v-if="value.url" @click="copy(value.fullUrl)"
                class="cursor-pointer rounded-xl border border-zinc-200 dark:border-zinc-700 bg-white dark:bg-zinc-800 p-3 hover:shadow-lg transition">
                <div
                    class="h-40 flex items-center justify-center bg-zinc-100 dark:bg-zinc-700 rounded-lg overflow-hidden">
                    <!-- 图片 -->
                    <img v-if="value.type === 'image'" :src="value.url" class="max-w-full max-h-full object-contain" />

                    <!-- 视频 -->
                    <video v-else-if="value.type === 'video'" :src="value.url" controls
                        class="max-w-full max-h-full object-contain" />

                    <!-- 音频 -->
                    <audio v-else-if="value.type === 'audio'" :src="value.url" controls class="w-full" @click.stop />

                    <!-- 其他 -->
                    <span v-else class="text-2xl">📦</span>
                </div>

                <div class="mt-2 text-sm truncate">{{ value.name }}</div>
                <div class="text-xs text-zinc-500">点击卡片复制链接</div>
            </div>

            <!-- 目录 -->
            <div v-else class="mt-6">
                <h2 class="font-semibold text-lg mb-3">📂 {{ key }}</h2>
                <div class="grid grid-cols-[repeat(auto-fill,minmax(220px,1fr))] gap-4">
                    <AssetTree :node="value" @copy="copy" />
                </div>
            </div>
        </template>
    </div>
</template>
