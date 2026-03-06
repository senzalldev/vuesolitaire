<template>
    <div v-if="status == 'running'" class="absolute bottom-0 overflow-hidden rounded-lg bg-[#0f0c1a]/90 border border-[#2e2650] shadow-lg flex items-center text-sm p-2 gap-2">
        <div class="flex items-center rounded bg-[#1e1836] text-[#b8922a] text-xs font-semibold px-3 py-1 tracking-wider uppercase">Stats</div>
        <div class="flex items-center gap-4 px-3 text-[#e8d5a3] text-xs">
            <div>Moves <span class="text-[#d4aa4a] font-semibold">{{ moves }}</span></div>
            <div>Points <span class="text-[#d4aa4a] font-semibold">{{ points }}</span></div>
            <div>Time <span class="text-[#d4aa4a] font-semibold">{{ time() }}</span></div>
        </div>
    </div>
</template>
<script setup lang="ts">
import { computed, inject, onMounted, onUnmounted, ref } from 'vue';
import { GameContextTag } from '../composables/GameContext';
import { Util } from '../game/Util';

const gameContext = inject(GameContextTag)!
const status = computed(() => gameContext.state.value.status)

const moves = computed(() => gameContext.state.value.stats.moves)
const points = computed(() => gameContext.state.value.stats.moves)

const updateTrigger = ref(false)

const time = () => Util.formatMillisAsHoursMinutesSecondsWithPadding(new Date().getTime() - gameContext.state.value.stats.startTime 
    + (updateTrigger.value?0:0))

onMounted(() => {
    const interval = setInterval(() => {
        updateTrigger.value = !updateTrigger.value
    }, 500)
    onUnmounted(() => clearInterval(interval))
})

</script>
