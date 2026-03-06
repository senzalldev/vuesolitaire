<template>
    <div class="bg-[#0f0c1a]/90 backdrop-blur border-b border-[#2e2650] px-4 py-2 flex items-center gap-3 shrink-0">
        <div class="flex items-center gap-2 select-none">
            <span class="text-[#e8d5a3] text-lg font-bold tracking-tight">
                <span class="text-[#b8922a]">Senzall's</span> Solitaire
            </span>
            <span class="text-[#6b5a8a] text-xs font-normal hidden sm:inline">Tyrian Edition</span>
        </div>
        <div class="grow"></div>
        <nav class="flex items-center gap-3">
            <a href="#" class="text-[#a89bc4] hover:text-[#e8d5a3] text-sm tracking-tight transition-colors"
                @click.prevent="$emit('menuAction', 'game-new')">New Game</a>
            <a href="#" class="text-[#a89bc4] hover:text-[#e8d5a3] text-sm tracking-tight transition-colors"
                @click.prevent="$emit('menuAction', 'game-stop')">Quit</a>
            <a href="#" class="text-[#a89bc4] hover:text-[#e8d5a3] text-sm tracking-tight transition-colors"
                @click.prevent="$emit('menuAction', 'about')">About</a>
            <a href="https://senzall.com" target="senzall"
                class="text-[#a89bc4] hover:text-[#e8d5a3] text-sm tracking-tight transition-colors hidden sm:inline">senzall.com ↗</a>
            <a href="https://ko-fi.com/senzall" target="_blank" rel="noopener noreferrer">
                <img src="/support_me_on_kofi_dark.png" alt="Support me on Ko-fi" class="h-8" />
            </a>
        </nav>
    </div>
</template>
<script setup lang="ts">

defineEmits<{
    menuAction: [action: string]
}>()

</script>
