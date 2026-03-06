<template>
    <div class="h-1 grow shrink flex flex-col">
        <Menubar @menuAction ="handleMenubarAction" />
        <div class="relative bg-[#0a0812] h-1 grow shrink flex flex-col overflow-hidden">
            <div v-if="height < 400"
                class="flex grow flex-col items-center justify-center text-center bg-[#0a0812] font-bold p-8 z-[1000]">
                <div class="text-5xl text-[#b8922a] mb-4">⚔️</div>
                <div class="text-2xl text-[#e8d5a3]">Please use a taller window</div>
                <div class="text-sm text-[#6b5a8a] mt-2">Your battlefield needs more height</div>
            </div>
            <GameRenderer v-else ></GameRenderer>

            <template v-if="gameStopped">
                <div class="top-0 bottom-0 right-0 left-0 insets-0 absolute bg-black opacity-40 flex flex-col justify-center items-center" >
                </div>
                <div class="top-0 bottom-0 right-0 left-0 insets-0 absolute bg-transparent flex flex-col justify-center items-center" >
                    <GridImage></GridImage>
                </div>
            </template>
            <StatsPanel></StatsPanel>
            <div v-if="gameWon" class="inset-0 absolute flex flex-col items-center justify-center z-20">
                <div class="absolute inset-0 bg-black opacity-70"></div>
                <StampedBanner text="Victory!"></StampedBanner>
                <div class="text-[#e8d5a3] text-3xl z-50 pt-16 tracking-tighter font-bold">
                    Score: <span class="text-[#d4aa4a]">{{ points }}</span> &nbsp;·&nbsp; Moves: <span class="text-[#d4aa4a]">{{ moves }}</span>
                </div>
            </div>
        </div>
        <ModalDialog :show="aboutShown" :onClose="() => aboutShown = false" title="Senzall's Solitaire — Tyrian Edition">
            <div class="flex flex-col gap-2">
                <p>Klondike Solitaire, forged in the fires of Tyria. Cards are divs — no Canvas, no compromise.</p>
                <p>GW2 theme by <ExternalLink href="https://senzall.com">Senzall</ExternalLink> · Engine by Stepan Rutz · WTFPL license.</p>
                <div class="h-2"></div>
                <p>Card images from <ExternalLink href="https://deckofcardsapi.com/">Deck of Cards API</ExternalLink></p>
                <p>Built with: TypeScript, Vue 3, Vite, Tailwind CSS v4</p>
                <p class="text-xs text-gray-500">Guild Wars 2 is a trademark of ArenaNet, LLC. This is unofficial fan content.</p>
                <div class="flex flex-col bg-black p-4 self-stretch items-center relative min-h-64">
                    <StyledImage v-for="(card,index) in aboutCards" :key="index" :imageSource="'/cards/' + card + '.svg'" :style="getAboutStyle(index)">
                    </StyledImage>
                </div>
            </div>
        </ModalDialog>
        <ConfirmDialog
                    :show="newGameConfirmShown"
                    :onCancel="() => newGameConfirmShown = false"
                    :onConfirm="confirmNewGame"
                    title="Confirm New Game">
                <p>Really start a new game?</p>
            </ConfirmDialog>
            <ConfirmDialog 
                    :show="stopGameConfirmShown"
                    :onCancel="() => stopGameConfirmShown = false"
                    :onConfirm="() => {
                        stopGameConfirmShown = false
                        gameContext.dispatch({ type: 'game-stop' })
                    }"
                    title="Confirm Stop Game">
                <p>Really end the current game?</p>
            </ConfirmDialog>
    </div>
</template>
<script setup lang="ts">
import { computed, CSSProperties, inject, onMounted, onUnmounted, ref } from 'vue'
import GameRenderer from '../components/GameRenderer.vue'
import GridImage from '../components/GridImage.vue'
import Menubar from '../components/Menubar.vue'
import { GameContextTag } from '../composables/GameContext'
import { useUrlState } from '../composables/UrlState'
import { useWindowSize } from '../composables/WindowSize'
import ConfirmDialog from './dialogs/ConfirmDialog.vue'
import ExternalLink from './dialogs/ExternalLink.vue'
import ModalDialog from './dialogs/ModalDialog.vue'
import StampedBanner from './stampedbanner/StampedBanner.vue'
import StatsPanel from './StatsPanel.vue'
import StyledImage from './StyledImage.vue'


const { height } = useWindowSize()
const gameContext = inject(GameContextTag)!

useUrlState(gameContext)

const aboutShown = ref(false)
const newGameConfirmShown = ref(false)
const stopGameConfirmShown = ref(false)

const confirmNewGame = () => {
    newGameConfirmShown.value = false
    gameContext.dispatch({ type: 'game-stop' }) 
    setTimeout(startNewGame, 500)
}

const handleMenubarAction = (action: string) => {
    if ("about" == action) {
        aboutShown.value = true
    } else if ("game-new" == action) {
        if (gameContext.state.value.status == "running") {
            newGameConfirmShown.value = true
        } else {
            startNewGame()
        }
    } else if ("game-stop" == action) {
        if (gameContext.state.value.status == "running") {
            stopGameConfirmShown.value = true
        } else if (gameContext.state.value.status == "launching") {
            gameContext.dispatch({ type: "game-stop" })
        }
    }
}

const startNewGame = () => {
    gameContext.dispatch({ type: "game-new" })
    setTimeout(() => {
        gameContext.dispatch({ type: "game-launched" })
    }, 1_000)
}
const angle = ref(0)

const getAboutStyle = (index: number) => ({ 
    transition: "all 5s ease-in-out", transformOrigin: "bottom center", 
    transform: "rotate(" + (angle.value * (index - aboutCards.length / 2)) + "deg)" } satisfies CSSProperties
)

/* simple animation */
const aboutCards = [ "0H", "JS", "QD", "KH", "AS", "AD" ]
const frame = ref(0)
onMounted(() => {
    if (gameContext.state.value.status === "stopped") {
        startNewGame()
    }
    const i = setInterval(() => {
        if (frame.value == 0) {
            angle.value = 12
        } else if (frame.value == 5) {
            angle.value = 0
        }
        frame.value++
        if (frame.value == 10) {
            frame.value = 0
        }
    }, 1_000)
    onUnmounted(() => clearInterval(i))
})
const gameStopped = computed(() => gameContext.state.value.status === "stopped")
const gameWon = computed(() => gameContext.state.value.status === "won")
const points = computed(() => gameContext.state.value.stats.points)
const moves = computed(() => gameContext.state.value.stats.moves)


</script>