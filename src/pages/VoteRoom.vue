<script setup lang="ts">
import { computed, watch, ref } from 'vue'
import { useRouter } from 'vue-router'
import BaseButton from '../components/BaseButton.vue'
import BasePlayerListItem from '../components/BasePlayerListItem.vue'
import { 
  gameStatus, 
  isMyRoleLeader, 
  isSubmittedVotePlayer, 
  myPeer, 
  players, 
  secondVotingPlayers, 
  selectedPlayer, 
  sendDataToRoomLeader, 
  setSelectedPlayer, 
  votingPlayers,
  voteResults 
} from '../store'
import type { GamePlayer } from '../model'

const router = useRouter()
const showResults = ref(false) // เพิ่ม state เพื่อตรวจสอบการแสดงผล

const currentVotingPlayers = computed(() =>
  secondVotingPlayers.value.length > 0
    ? secondVotingPlayers.value
    : votingPlayers.value
)

const previewCurrentVotingPlayers = computed(() => filterPlayersPeer(currentVotingPlayers.value, myPeer.id))
const otherPlayers = computed(() => filterPlayersPeer(players.value, myPeer.id))
const disabledSubmit = computed(() => !selectedPlayer.value || isSubmittedVotePlayer.value)

watch(gameStatus, () => {
  if (gameStatus.value === 'gameResultPhase') {
    showResults.value = true // กำหนดให้แสดงผลลัพธ์เมื่อถึงเฟสผลลัพธ์
    router.push('/results')
  }
})

const submitVote = () => {
  if (!selectedPlayer.value) return
  sendDataToRoomLeader({ type: 'vote', target: selectedPlayer.value })
  showResults.value = true // อัปเดตให้แสดงผลทันทีหลังโหวต
}
</script>

<template>
  <div>
    <h1>Vote Room</h1>
    <div v-if="!showResults">
      <BasePlayerListItem
        v-for="player in otherPlayers"
        :key="player.id"
        :player="player"
        @click="setSelectedPlayer(player)"
      />
      <BaseButton :disabled="disabledSubmit" @click="submitVote">Submit Vote</BaseButton>
    </div>
    <div v-else>
      <h2>Voting Results</h2>
      <ul>
        <li v-for="(count, player) in voteResults" :key="player">
          {{ player }}: {{ count }} votes
        </li>
      </ul>
    </div>
  </div>
</template>
