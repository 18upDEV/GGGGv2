<script setup lang="ts">
import { useRouter } from 'vue-router'
import BaseButton from '../components/BaseButton.vue'
import BasePlayerListItem from '../components/BasePlayerListItem.vue'
import { gameResult, insiderPlayer, isMyRoleLeader, myRole, players, quitGame, winnerSide } from '../store'

const router = useRouter()

const isMyRoleInsider = myRole.value === 'insider'

const votingResultPlayers = players.value.filter(player => player.votingPlayers && player.votingPlayers.length > 0)

function handleNewGame() {
  quitGame()
  router.push({ name: 'home' })
}
</script>

<template>
  <template v-if="isMyRoleLeader">
    <div
      class="text-6xl"
    >
      Winner is
    </div>
    <div class="text-6xl font-bold uppercase text-primary">
      {{ winnerSide }}
    </div>
  </template>
  <div
    v-else
    class="text-6xl font-bold uppercase"
    :class="{ 'text-primary': gameResult === 'win' }"
  >
    {{ gameResult }}
  </div>

  <div v-if="isMyRoleInsider" class="text-4xl">
    {{ gameResult === 'win' ? 'You are disguised from Villagers.' : 'You have been found.' }}
  </div>
  <template v-else>
    <div class="text-4xl font-bold uppercase">
      The <span class="font-bold text-primary">INSIDER</span> is
    </div>

    <div
      class="mt-4 w-full max-w-xs rounded-xl border-2 border-dashed border-primary p-4 text-center text-6xl text-white"
    >
      {{ insiderPlayer.playerName }}
    </div>
    <template>
  <div class="flex w-full max-w-xs flex-col gap-4 pt-4">
    <div>
      Voting Result
    </div>

    <div v-for="player of votingResultPlayers" :key="player.peer" class="flex flex-col gap-1">
      <BasePlayerListItem :player="player">
        {{ player.playerName }}
        <span class="uppercase" :class="{ 'text-primary': player.role === 'insider' }">
          ({{ player.role }})
        </span>
      </BasePlayerListItem>
      <div class="text-xs">
        {{ player.votingPlayers.map((player) => player.playerName).join(', ') }}
      </div>
    </div>

    <BaseButton class="mt-4" @click="playAgain">
      Play Again
    </BaseButton>
    <BaseButton class="mt-4" @click="goToGameRoom">
      Back to Game Room
    </BaseButton>
  </div>
</template>

<script setup lang="ts">
import { useRouter } from 'vue-router'
import { ref } from 'vue'
import { players, votingPlayers, gameStatus, gameAnswer, gameResult, isSubmittedVotePlayer, selectedPlayer, secondVotingPlayers } from '../store'
import BasePlayerListItem from '../components/BasePlayerListItem.vue'

const router = useRouter()
const votingResultPlayers = ref([]) // สมมติว่าคุณมีข้อมูลนี้

const playAgain = () => {
  // รีเซ็ตสถานะที่จำเป็นสำหรับการเล่นเกมใหม่ในห้องเดิม
  votingPlayers.value = []
  selectedPlayer.value = null
  secondVotingPlayers.value = []
  gameStatus.value = 'waiting' // หรือสถานะที่เหมาะสมสำหรับการเริ่มเกมใหม่
  gameAnswer.value = ''
  gameResult.value = null
  isSubmittedVotePlayer.value = false

  // คุณสามารถเพิ่มลอจิกเพิ่มเติมที่จำเป็นสำหรับการเริ่มเกมใหม่ที่นี่
  router.push({ name: 'game-room' })
}

const goToGameRoom = () => {
  // รีเซ็ตสถานะที่จำเป็น
  players.value = []
  votingPlayers.value = []
  gameStatus.value = null
  gameAnswer.value = ''
  gameResult.value = null
  isSubmittedVotePlayer.value = false

  router.push({ name: 'game-room' })
}
</script>

<style scoped>
/* เพิ่มสไตล์ของคุณที่นี่ถ้าจำเป็น */
</style>