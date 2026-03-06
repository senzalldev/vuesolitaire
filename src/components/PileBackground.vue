<template>
    <div :data-pile="pileId" class="flex items-center cursor-pointer select-none absolute" :style="mainStyle" >
        <div class="bg-[#0d0b18] border-2 border-dashed border-[#b8922a] rounded-lg flex justify-center items-center text-[#b8922a] opacity-50 capitalize text-xl font-semibold tracking-widest"
            :style="contentStyle">{{ label}}</div>
    </div>

</template>
<script setup lang="ts">
import { computed, CSSProperties, inject } from 'vue';
import { getPilePosition, RendererContextTag } from '../composables/RendererContext';
import { Pile } from '../game/GameTypes';
import { GameUtil } from '../game/GameUtil';

const { pile } = defineProps<{
    pile: Pile,
}>()

const rendererContext = inject(RendererContextTag)!
const { geometry, availableSize } = rendererContext

/* show a label on the background for some piles */
const label = ["table", "waste"].indexOf(pile.type) == -1 ? pile.type : ""

const pileId = GameUtil.pileId(pile)
const mainStyle = computed(() => {
    const position = getPilePosition(availableSize.value, geometry.value, pile)
    return {
        left: position.x + "px",
        top: position.y + "px",
    } satisfies CSSProperties
})
const contentStyle = computed(() => {
    return {
        width: geometry.value.cardWidth + "px",
        height: geometry.value.cardHeight + "px",
    } satisfies CSSProperties
})
</script>

