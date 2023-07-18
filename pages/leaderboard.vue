<template>
  <div id="canvas">
    <div id="deep-space" />
    <div id="board">
      <h1>Ranking do Jogo</h1>
      <ul>
        <li v-for="(player, index) in ranking" :key="index">
          {{ player.rankPosition }}º Lugar - Pontuação: {{ player.points }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script >
// Como a ideia do EP é mexer no front, estou usando um json mockado no lugar de criar um endpoint novo no back que devolva esse json
import leaderboard from "../assets/Leaderboard.json"
export default {
  data: () => ({
    ranking: leaderboard
  }),
}
</script>

<style>
#board {
  position: absolute;
  opacity: 80%;
  padding: 2%;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: white;
}
#canvas {
  height: calc(100vh - 4rem);
  width: calc(100vw - 4rem);

  padding: 2rem;

  background-color: #36bbf5;
  overflow: hidden;

  position: relative;

  display: flex;
  justify-content: center;
  align-items: center;
}

@keyframes slide {
  0% {
    transform: translate(1px);
  }
  50% {
    transform: translate(-1px);
  }
  100% {
    transform: translate(1px);
  }
}

#deep-space {
  height: calc(100% - 4rem);
  width: calc(100% - 4rem);

  background-image: url("~/assets/space.png");
  background-origin: content-box;
  animation: slide 3s linear infinite;

  position: absolute;
  z-index: 0;
}

</style>
