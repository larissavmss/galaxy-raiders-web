<template>
  <div id="canvas">
    <div id="deep-space" />
    <div id="space-field">
      <div v-show="menu" class="myMenu">
        <button @click="startGame">Iniciar</button>
        <button @click="showScore">Placar</button>
        <button @click="endGame">Encerrar jogo</button>
      </div>
      <SpaceObject id="spaceship" class="spaceship" :data="spaceField.ship" resolution="2" v-if="!menu"/>

      <SpaceObject class="asteroid" :data="asteroid" resolution="2" v-if="!menu"
                  :key="asteroid.center"
                  v-for="asteroid in spaceField.asteroids" />

      <SpaceObject class="missile" :data="missile" resolution="2" v-if="!menu"
                  :key="missile.center"
                  v-for="missile in spaceField.missiles" />

      <SpaceObject class="explosion" :data="explosion" resolution="2" v-if="!menu"
                  :key="explosion.center"
                  v-for="explosion in spaceField.explosions" />
    </div>
  </div>
</template>

<script setup>
let menu = true;
let intervalId = null;
const {
  data: spaceField,
  refresh: updateSpaceField
} = await $get("/space-field");

onMounted(() => {
  window.addEventListener("keydown", async (event) => {
    const keyToCommand = {
      "ArrowUp": "MOVE_SHIP_UP",
      "ArrowDown": "MOVE_SHIP_DOWN",
      "ArrowRight": "MOVE_SHIP_RIGHT",
      "ArrowLeft": "MOVE_SHIP_LEFT",
      "Space": "LAUNCH_MISSILE",
      "Escape": "PAUSE_GAME",
    };

    const command = keyToCommand[event.code];

    // Ignore if invalid key was pressed
    if (command === undefined) return;

    if(command === "PAUSE_GAME"){
      // O jogo é retomado/pausado quando a tecla "esc" é apertada
      menu = !menu;

      console.log(menu);
      if(menu){
        clearInterval(intervalId);
      } else {
        intervalId = window.setInterval(updateSpaceField, 1000);
      }
    }
    if(!menu){
      console.log(`Triggering command: ${command}`);
      await $post("/ship/commands", { command });
    }
  });
})
function startGame() {
  // Estou considerando que iniciar o jogo é apenas continuar, a não ser que a pessoa clique em encerrar antes
  console.log("Iniciando o jogo"); 
  menu = false;
  intervalId = window.setInterval(updateSpaceField, 1000);
}

async function endGame() {
  console.log("Encerrando o jogo");
  // No meu EP2 eu considerei que meu jogo é encerrado quando o comando de pausar é executado
  const command = "PAUSE_GAME";
  await $post("/ship/commands", { command });
}

function showScore() {
  console.log("Exibindo o placar");
  // Exibir placar
}

</script>

<style>
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

#space-field {
  height: calc(100% - 4rem);
  width: calc(100% - 4rem);

  position: relative;
}

.spaceship {
  background-image: url("~/assets/spaceship.png");
}

.asteroid {
  background-image: url("~/assets/asteroid.png");
}

.missile {
  background-image: url("~/assets/missile.png");
}

.explosion {
  background-image: url("~/assets/explosion.png");
}
.myMenu {
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

.myMenu button {
  margin-bottom: 10px;
}
</style>
