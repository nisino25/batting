<template>
  <div id="app" class="container-fluid text-center">
    <!-- <h1 class="my-3">Batting Game</h1> -->

    <!-- Name Input Modal -->
    <div v-if="modalShow" class="modal-overlay d-flex justify-content-center align-items-center">
      <div class="modal-content p-4 bg-white border rounded">
        <template v-for="(player, index) in players" :key="index" >
          <div class="mb-3">
            <label :for="'playerName' + index" class="form-label">
              Player {{ index + 1 }} Name:
            </label>
            <input
              type="text"
              class="form-control"
              :id="'playerName' + index"
              v-model="player.name"
            />
          </div>

        
        </template>
        <button class="btn btn-primary" @click="closeModal()">Save</button>
      </div>
    </div>

    <template v-else>
  
      <!-- Player Controllers -->
      <div class="controllers">
        <template v-for="(player, index) in players" :key="index">
          <div class="controller border p-3" :id="`Tile-${player.name}`">
            <h4 style="text-align: left;">{{ player.name }}</h4>
            <div class="vault-conatainer">
              <div v-for="(gem, gemIndex) in player.tempVault" :key="gemIndex" class="gem" :style="`background: ${gem.color}`"></div>
            </div>
            <hr>
            <tempalte v-if="gameStatus !=='finished'">
              <template v-if="player.picked">
                <h3 style="color: crimson;">選び終わりました</h3>
              </template>
              <template v-else-if="player.tempPicked">
                {{ player.tempPicked }}でよろしいですか<br>
                <div class="flex-conainer" style="gap: 10px; display: flex; justify-content: center; margin-top: 1em;">
                  <div class="badge bg-success" @click="confirmPick(player)">はい</div>
                  <div class="badge bg-danger" @click="player.tempPicked = null">いいえ</div>
                </div>
              </template>
              <template v-else>
                <div class="flex-container tile-badge-container">
                  <template v-for="(index, tile) in tiles" :key="index">
                    <div class="badge bg-info" @click="tempPick(player, tile+1)">Tile-{{tile + 1}}</div>
                  </template>
                </div>
                <div class="flex-container">
                  <template v-for="playerBadge in players" :key="playerBadge">
                    <div class="badge bg-danger" @click="tempPick(player, playerBadge.name);">{{playerBadge.name}}</div>
                  </template>
                </div>
              </template>
            </tempalte>
            <template v-else>
              <h3>総合得点: {{ player.finalScore }}</h3>
            </template>
            <hr>
            <div class="vault-conatainer">
              <div v-for="(gem, gemIndex) in player.permanentVault" :key="gemIndex" class="gem" :style="`background: ${gem.color}`"></div>
            </div>
            <!-- <p>Points: {{ player.points }}</p> -->
          </div>
        </template>
      </div>

      <!-- Name tile container -->
      <template v-for="(player, index) in players" :key="index">
        <div
          v-if="observedElements[player.name]"
          class="circle-tile"
          :style="getNameTileStyle(player,index+1)"
        >
          <span>{{ player.name.slice(0, 4) }}</span>
        </div>
      </template>

  
      <!-- Tiles Section -->
      <div class="tiles">
        <template v-for="(tile, index) in tiles" :key="index">
          <div class="tile border rounded p-3 m-2"  :id="`Tile-${index+1}`">
            <p>Tile-{{ index+1 }}</p>
            <div class="gems-container">
              <div
                v-for="(gem, gemIndex) in tile"
                :key="gemIndex"
                class="gem"
                :style="`background: ${gem.color}`"
                >
              </div>
            </div>
          </div>
        </template>
        <div class="tile border rounded p-3 m-2" style="grid-column: span 2; width: unset;">
          <p>ジェム残り<strong>{{totalGems.length}}個</strong>({{ gameStatus }})</p>
          <table  class="table table-bordered text-center">
            <tbody>
              <tr>
                <th><div><div>青</div></div> </th>
                <th><div><div>黄</div></div></th>
                <th><div><div>赤</div></div></th>
                <th><div><div>３つセット</div></div></th>

              </tr>
              <tr>
                <td><div>1</div></td>
                <td><div>2</div></td>
                <td><div>3</div></td>
                <td><div>+4</div></td>
              </tr>
            </tbody>
          </table>
          <table  class="table table-bordered text-center" style="margin-bottom: unset;">
            <tbody>
              <tr>
                <th style="padding: .2em !important;"><div><div>白の数</div></div> </th>
                <th><div><div>1</div></div></th>
                <th><div><div>2</div></div></th>
                <th><div><div>3</div></div></th>
                <th><div><div>4</div></div></th>
                <th><div><div>5</div></div></th>
                <th><div><div>6</div></div></th>
              </tr>
              <tr>
                <td><div>点数</div></td>
                <td><div>1</div></td>
                <td><div>4</div></td>
                <td><div>9</div></td>
                <td><div>16</div></td>
                <td><div>25</div></td>
                <td><div>36</div></td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </template>

  </div>
</template>

<script>
export default {
  data() {
    return {
      isDeveloping: true,
      modalShow: true, // Controls modal visibility
      totalGems: [],
      tiles: [],
      // tiles: [[],[],[],[]],
      players: [
        // { name: "Ruriko", tempVault: [], permanentVault: [], tempPicked: null, picked: null, finalScore: null  },
        // { name: "Koki", tempVault: [], permanentVault: [], tempPicked: null, picked: null, finalScore: null  },
        // { name: "Shin", tempVault: [], permanentVault: [], tempPicked: null, picked: null, finalScore: null  },
        { name: "Nozo", tempVault: [], permanentVault: [], tempPicked: null, picked: null, finalScore: null  },
        { name: "Becca", tempVault: [], permanentVault: [], tempPicked: null, picked: null, finalScore: null  },
        // { name: "Misaki", tempVault: [], permanentVault: [], tempPicked: null, picked: null, finalScore: null  },
        // { name: "Minori", tempVault: [], permanentVault: [], tempPicked: null, picked: null, finalScore: null  },
      ],
      gameStatus: 'registering',
      observedElements: {},
    };
  },
  methods:{
    sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms));
    },

    localTest(){
      if(this.isDeveloping){
        this.closeModal()
        this.endOfRound();
      }
    },
    closeModal(){
      this.modalShow = false;
      
      for (let i = 0; i < this.players.length -1; i++) {
        this.tiles.push([]);
      }

      let baseNum = 6
      // let baseNum = -1
      
      let numDiff = (this.players.length - 2)
      baseNum = numDiff * 3 + baseNum

      this.totalGems = [
        ...Array(baseNum).fill("Blue"),
        ...Array(baseNum).fill("Yellow"),
        ...Array(baseNum).fill("Red"),
        ...Array(6).fill("White"),
      ];

      this.gameStatus = 'selecting'

    },
    endOfRound(){
      // end game if there is no gems left
      if(this.totalGems.length == 0) return this.finishGame();
      
      // Redistribute gems to tiles
      this.tiles.forEach((tile) => {
        if (tile.length === 0) {
          // Add two gems if the tile is empty
          for (let i = 0; i < 2; i++) {
            const gem = this.getRandomGem();
            if (gem) tile.push({ color: gem });
          }
        } else {
          // Add one gem to tiles with existing gems
          const gem = this.getRandomGem();
          if (gem) tile.push({ color: gem });
        }
      });


    },
    getRandomGem(){
      if(this.totalGems.length == 0) return false
      const randomIndex = Math.floor(Math.random() * this.totalGems.length);
      const gem = this.totalGems[randomIndex];
      this.totalGems.splice(randomIndex, 1); // Remove the gem from the array
      return gem; // Return the selected gem
    },

    initializeObservers() {
      this.players.forEach((player) => {
        const selector = `#Tile-${player.name}`;
        const observer = new MutationObserver(() => {
          this.updateElementExistence(player.name, selector);
        });
        const target = document.body;
        observer.observe(target, {
          childList: true,
          subtree: true,
        });
        this.updateElementExistence(player.name, selector); // Initial check
      });
    },
    updateElementExistence(name, selector) {
      this.observedElements = {
        ...this.observedElements,
        [name]: document.querySelector(selector) !== null,
      };
    },
    getNameTileStyle(player,index) {
      // const destination = this.getCenter(`#Tile-${name} .flex-container`);
      let destination
      if(this.gameStatus == 'selecting'){
        destination = this.getCenter(`#Tile-${player.name} h4`);
      }else if(this.gameStatus == 'showing target'){
        destination = this.getTarget(`#${player.picked}`,index);
      }
      if (!destination) {
        return '';
      }
      return `
        top: ${destination.y}px;
        left: ${destination.x}px;
      `;
    },
    getCenter(query) {
      const element = document.querySelector(query);
      if (element) {
        const rect = element.getBoundingClientRect();
        const centerX = rect.left + rect.width / 2;
        const centerY = rect.top + rect.height / 2;
        return { x: centerX, y: centerY };
      } else {
        console.error(`Element with query "${query}" not found.`);
        return null;
      }
    },
    getTarget(query,index) {
      const element = document.querySelector(query);
      if (element) {
        const rect = element.getBoundingClientRect();
        const portion = index / this.players.length
        const centerX = rect.left + rect.width * portion - 20;
        const centerY = rect.top + rect.height / 2;
        return { x: centerX, y: centerY };
      } else {
        console.error(`Element with query "${query}" not found.`);
        return null;
      }
    },

    tempPick(player,tile){
      // if(this.isDeveloping) return player.picked = `Tile-${tile}`
      player.tempPicked = `Tile-${tile}`
      if(this.isDeveloping) this.confirmPick(player)
    },
    confirmPick(player){
      player.picked = player.tempPicked
      if(this.unpickedNum == 0) this.checkTarget()
    },
    async checkTarget(){
      await this.sleep(1000)
      this.gameStatus = 'showing target'
      await this.sleep(2500)

      // Group players by their picked target
      const targetMap = {};
      this.players.forEach((player) => {
        const target = player.picked;
        if (!targetMap[target]) {
          targetMap[target] = [];
        }
        targetMap[target].push(player);
      });

      console.log(targetMap)

      // Process each target
      Object.entries(targetMap).forEach(([target, players]) => {
        // Special case: If the target is the player's own vault, they get it no matter what
        const selfSelectingPlayers = players.filter(
          (player) => target === `Tile-${player.name}`
        );

        selfSelectingPlayers.forEach((player) => {
          // Move gems from tempVault to permanentVault
          player.permanentVault.push(...player.tempVault);
          player.tempVault = [];
        });

        // Exclude self-selecting players from further processing
        players = players.filter(
          (player) => !selfSelectingPlayers.includes(player)
        );

        if (players.length > 1) {
          // Overlap: No one gets the vault
          console.log(`Conflict at ${target}, no one gets anything.`);
          return;
        }

        const player = players[0]; // Single player for this target

        if (/^Tile-\d+$/.test(target)) {
          // Public Tile logic: target is something like "Tile-1", "Tile-2", etc.
          const tileIndex = parseInt(target.replace("Tile-", ""), 10) - 1;
          const gems = this.tiles[tileIndex];
          if (gems && gems.length > 0) {
            if (!player.tempVault) {
              player.tempVault = []; // Ensure tempVault is initialized
            }
            player.tempVault.push(...gems);
            this.tiles[tileIndex] = []; // Clear the tile
          }
        } else if (target.startsWith("Tile-")) {
        // Player-specific tile logic: target is something like "Tile-nozo", "Tile-ando", etc.

        if (!player) {
          console.error(`Player is undefined for target ${target}.`);
          return; // Exit early if player is undefined
        }

        const targetPlayerName = target.replace("Tile-", ""); // Extract the player name
        const targetPlayer = this.players.find(p => p.name === targetPlayerName);

        if (!targetPlayer) {
          console.error(`Player with name ${targetPlayerName} not found.`);
          return; // Exit early if no target player is found
        }

        if (player.name === targetPlayer.name) {
          // Own vault: Move gems to permanent vault (already handled in selfSelectingPlayers)
        } else {
          // Other's vault: Steal from tempVault
          if (!player.tempVault) {
            player.tempVault = [];
          }
          player.tempVault.push(...targetPlayer.tempVault);
          targetPlayer.tempVault = [];
        }
      }


      });

      await this.sleep(2500)


      // Reset for next round
      this.players.forEach((player) => {
        player.picked = null;
        player.tempPicked = null;
      });
      this.gameStatus = 'selecting';
      this.endOfRound();
      
    },
    finishGame(){
      alert('finsihed')
      this.players.forEach((player) => {
        // Combine tempVault and permanentVault
        const allGems = [...player.tempVault, ...player.permanentVault];

        // Count gems by color
        const gemCounts = allGems.reduce(
          (counts, gem) => {
            if (gem.color === "Blue") counts.blue++;
            else if (gem.color === "Yellow") counts.yellow++;
            else if (gem.color === "Red") counts.red++;
            else if (gem.color === "White") counts.white++;
            return counts;
          },
          { blue: 0, yellow: 0, red: 0, white: 0 }
        );

        // Calculate points for individual colors
        let score =
          gemCounts.blue * 1 +
          gemCounts.yellow * 2 +
          gemCounts.red * 3;

        // Calculate points for sets of three (1 blue + 1 yellow + 1 red)
        const setsOfThree = Math.min(
          gemCounts.blue,
          gemCounts.yellow,
          gemCounts.red
        );
        score += setsOfThree * 4;

        // Calculate points for white gems
        const whitePoints = [0, 1, 4, 9, 16, 25, 36]; // Points for 0-6 white gems
        score += whitePoints[Math.min(gemCounts.white, 6)];

        // Set the player's final score
        player.finalScore = score;
      });
      this.gameStatus = 'finished'
    }
  },
  mounted(){
    console.clear()

    this.initializeObservers();

    this.localTest()
  },
  computed: {
    unpickedNum() {
      return this.players.filter(player => !player.picked).length;
    },
  },

  
};
</script>

<style>
/* Modal styles */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  background: rgba(0, 0, 0, 0.5);
  z-index: 1000;
}
.modal-content {
  width: 300px;
}

/* Controller positioning */
.controllers {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);

  height: 95dvh; /* Adjust as needed */
  width: 95dvw;  /* Adjust as needed */
  /* margin: 0 auto; */

  /* pointer-events: none; */
}

.controller {
  position: absolute;
  /* display: inline-block; */
  background-color: lightgrey;
  /* padding: 10px; */
  width: auto;
  min-width: 245px;
  height: auto;
  /* padding: 1em; */
  transform-origin: center;
  border-radius: 10px;
}

.circle-tile{
  display: block;
  width: 40px;
  aspect-ratio: 1;
  background: black;
  color: white;
  border-radius: 50%;
  font-size: .8em;
  line-height: 1;
  opacity: .65;


  position: absolute;
  top: -50px;
  left: -50px;
  transform: translate(-50%,-50%);

  transition: all 1s ease-in-out;

  z-index: 50;
}

.circle-tile span{
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);
}


.flex-container{
  display: flex;
  justify-content: left;
  gap: 5px;
  align-items: center;
}

.tile-badge-container{
  margin-bottom: .5em;
}

.vault-conatainer{
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  justify-content: space-between;
  gap: 5px;
}

.vault-conatainer .gem{
  width: unset;
}

/* Positioning 5 controllers in a pentagon */
.controllers .controller:nth-child(1) {
  top: 0em;
  left: 0em;
  transform: rotate(180deg);
  /* transform: rotate(135deg); */

}
.controllers .controller:nth-child(2) {
  bottom: 0em;
  left: 0em;
  /* transform: rotate(40deg); */
}
.controllers .controller:nth-child(3) {
  bottom: 0em;
  right: 0em;
  /* transform: rotate(-40deg); */
}


.controllers .controller:nth-child(4) {
  top: 0em;
  right: 0em;
  transform: rotate(180deg);
}

/* .controllers .controller:nth-child(1) {
  top: 3em;
  left: 0em;
  transform: rotate(135deg);

}
.controllers .controller:nth-child(2) {
  bottom: 3em;
  left: 0em;
  transform: rotate(40deg);
}
.controllers .controller:nth-child(3) {
  bottom: 3em;
  right: 0em;
  transform: rotate(-40deg);
}


.controllers .controller:nth-child(4) {
  top: 3em;
  right: 0em;
  transform: rotate(225deg)
}

.controllers .controller:nth-child(5) {
  top: 30%;
  right: 0%;
  transform: translateY(-50%) translateX(25%) rotate(-90deg);
} */


.tiles {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);

  display: grid;
  grid-template-columns: repeat(2,1fr);

  z-index: 25;
  
  /* width: 120px; */
}

.tiles .tile{
  width: 175px;
  height: auto;
  padding: 1em;
  background-color: lightgrey;
}

.tiles .tile p{
  font-weight: bold;
}

.gems-container {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  justify-content: center;
}

/* Common styles for gems */
.gem {
  position: relative;
  width: 25%;
  aspect-ratio: 1;
  border: 1px solid black;
  /* transform: rotate(45deg); */
  /* border-radius: 50%; */
}

.small-gem{
  width: 15px;
  display: inline-block;
  margin: 5px;
}

.table>:not(caption)>*>*{
  padding: .3rem .3rem !important;
}
</style>
