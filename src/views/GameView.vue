
<template>
    <body>
        <br><br><br>
        <div id="player-popup" class="modal">
            <div class="modal-content">
                <span class="close" onclick="closePopup()">&times;</span>
                <h2>MADN Playerselection</h2>
                <form id="player-form">
                    <!-- Spielerinformationen und Farbauswahl für jeden Spieler -->
                    <div class="player-info">
                        <label for="player1" style="margin-right: 10px; margin-top:15px;">Player 1 :</label>
                        <input type="text" id="player1" required>
                        <div class="color-options">
                            <div class="color-option yellow" onclick="selectColor('yellow', 'color1')"></div>
                        </div>
                    </div>
                    <div class="player-info">
                        <label for="player2" style="margin-right: 10px; margin-top:15px;">Player 2 :</label>
                        <input type="text" id="player2" required>
                        <div class="color-options">
                            <div class="color-option green" onclick="selectColor('green', 'color1')"></div>
                        </div>
                    </div>
                    <div class="player-info">
                        <label for="player3" style="margin-right: 10px; margin-top:15px;">Player 3 :</label>
                        <input type="text" id="player3" required>
                        <div class="color-options">
                            <div class="color-option blue" onclick="selectColor('blue', 'color1')"></div>
                        </div>
                    </div>
                    <div class="player-info">
                        <label for="player4" style="margin-right: 10px; margin-top:15px;">Player 4 :</label>
                        <input type="text" id="player4" required>
                        <div class="color-options">
                            <div class="color-option red" onclick="selectColor('red', 'color1')"></div>
                        </div>
                    </div>
                
        
                    <button type="button" id="startButton" onclick="startGame()">Spiel starten</button>
                </form>
            </div>
        </div>
        

        <div id="game-board"></div>
    </body>

</template>

<script>
export default {
  data() {
    return {
      player1Name: '',
      player2Name: '',
      player3Name: '',
      player4Name: '',
      fieldList: [
        [0, 4], [1, 4], [2, 4], [3, 4], [4, 4], [4, 3], [4, 2], [4, 1], [4, 0], [5, 0], [6, 0], [6, 1], [6, 2], [6, 3], [6, 4], [7, 4], [8, 4], [9, 4], [10, 4], [10, 5], [10, 6], [9, 6], [8, 6], [7, 6], [6, 6], [6, 7], [6, 8], [6, 9], [6, 10], [5, 10], [4, 10], [4, 9], [4, 8], [4, 7], [4, 6], [3, 6], [2, 6], [1, 6], [0, 6], [0, 5]
      ],
      houseList: [
        [0, 0], [1, 0], [0, 1], [1, 1], [9, 0], [10, 0], [9, 1], [10, 1], [0, 9], [1, 9], [0, 10], [1, 10], [9, 9], [10, 9], [9, 10], [10, 10]
      ],
      pieceList: [
        [0, 0], [1, 0], [0, 1], [1, 1], [9, 0], [10, 0], [9, 1], [10, 1], [0, 9], [1, 9], [0, 10], [1, 10], [9, 9], [10, 9], [9, 10], [10, 10]
      ],
      goalList: [
        [1, 5], [2, 5], [3, 5], [4, 5], [5, 1], [5, 2], [5, 3], [5, 4], [5, 9], [5, 8], [5, 7], [5, 6], [9, 5], [8, 5], [7, 5], [6, 5]
      ],
      startingTileList: [
        [0, 4], [6, 0], [4, 10], [10, 6]
      ],
      playerColors: ['yellow', 'green', 'blue', 'red'],
      piecesOut: [0, 0, 0, 0],
      playerturn: 0,
      rolledDice: 1,
      playeramount: 0,
      timesPlayerRolled: 0,
      API_BASE_URL: "http://localhost:9000"
    };
  },
  methods: {
    openPopup() {
      const popup = document.getElementById('player-popup');
      popup.style.display = 'block';
    },
    closePopup() {
      const popup = document.getElementById('player-popup');
      popup.style.display = 'none';
    },
    async startGame() {
      this.player1Name = document.getElementById('player1').value;
      this.player2Name = document.getElementById('player2').value;
      this.player3Name = document.getElementById('player3').value;
      this.player4Name = document.getElementById('player4').value;

      this.playeramount = this.filledPlayer();
      await this.setPlayeramountInBackend(this.playeramount);

      this.initializeGame();

      console.log('Spieler 1:', this.player1Name);
      console.log('Spieler 2:', this.player2Name);
      console.log('Spieler 3:', this.player3Name);
      console.log('Spieler 4:', this.player4Name);

      this.closePopup();
    },
    adjustGameBoard(playeramount) {
      if (playeramount < 2 || playeramount > 4) {
        console.error("Ungültige Spieleranzahl. Die Spieleranzahl muss zwischen 2 und 4 Spielern liegen.")
      }
      const fieldsToDelete = (4 - playeramount) * 4;
      this.houseList.splice(-fieldsToDelete, fieldsToDelete);
    },
    filledPlayer() {
      let filledPlayer = 0;

      if (this.player1Name.trim() !== '') {
        filledPlayer++;
      }
      if (this.player2Name.trim() !== '') {
        filledPlayer++;
      }
      if (this.player3Name.trim() !== '') {
        filledPlayer++;
      }
      if (this.player4Name.trim() !== '') {
        filledPlayer++;
      }
      return filledPlayer;
    },
    async initializeGame() {
      await this.setPlayerTurnInBackend(0);
      await this.setPiecesListInBackend(this.pieceList);
      this.createGameBoard();
      const gameInterface = this.createGameInterface();
      document.getElementById('game-board').parentElement.appendChild(gameInterface);
      this.adjustGameBoard(this.playeramount);
      this.addStartPlayerCircles(this.houseList);
      this.setTimesPlayerRolledInBackend(0);
    },
    async addStartPlayerCircles(list) {
      for (let i = 0; i < list.length; i++) {
        const element = list[i];
        const firstValue = element[0];
        const secondValue = element[1];
        const cell = this.findCellByRowAndColumn(firstValue, secondValue);
        await this.getPlayerTurnFromBackend();
        if (i % 4 === 0) {
          if (i != 0) {
            await this.updatePlayerturn();
          }
        }
        await this.getPlayerTurnFromBackend();
        this.addPlayerCircle(cell, this.playerColors[this.playerturn]);
      }

      this.setPlayerTurnInBackend(0);
    },
    sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms));
    },
    createCell(className, row, column) {
      const cell = document.createElement('div');
      cell.className = className;
      cell.setAttribute('data-row', row);
      cell.setAttribute('data-column', column);

      cell.addEventListener('click', () => {
        if (!cell.classList.contains('empty')) {
          this.isMovingPieceOutAllowed(cell);
          this.movePiece(cell);
        }
      });
      return cell;
    },
    // ... (continue with other methods)
  },
  mounted() {
    const startButton = document.getElementById('startButton');
    startButton.addEventListener('click', this.openPopup);
  }
}
</script>




<style>
#game-board {
    display: grid;
    grid-template-columns: repeat(11, 40px);
    grid-template-rows: repeat(11, 40px);
    gap: 1px;
    background-color: #3a503b;
    padding: 5px;
    width: 460px;
    height: 460px;
    margin: auto; 
    margin-top: 20px;
    text-align: center; 
}

.tile, .house, .goal, .goalp1, .housep1,.goalp2, .housep2,.goalp3, .housep3,.goalp4, .housep4, .empty {
    width: 35px;
    height: 35px;
    margin: 5px;
    border: 2px solid black;
}

.tile {
    background-color: #fafafa;
}

.house {
    background-color: #1565c0;
}

.goal {
    background-color: #ff4500;
}

.empty {
    background-color: transparent;
    border: 1px solid transparent;
}

.housep1 {
    background-color: rgb(236, 243, 100);
}

.goalp1 {
    background-color: rgb(236, 243, 100);
}
.housep2 {
    background-color: rgb(55, 155, 55);
}

.goalp2 {
    background-color: rgb(55, 155, 55);
}
.housep3 {
    background-color: rgb(34, 90, 163);
}

.goalp3 {
    background-color: rgb(34, 90, 163);
}
.housep4 {
    background-color: rgb(230, 67, 67);
}

.goalp4 {
    background-color: rgb(230, 67, 67);
}

.player-circle {
    width: 20px;
    height: 20px;
    background-color: green;
    border-radius: 50%;
    margin: 7px;
    border: 3.5px solid black;
}

.game-interface {
    display:flex;
}

.dice-image {
    margin-right: 20px;
    margin-top: 7px;
    width: 100px;
    height: 100px;
}

.modal {
    display: none;
    position: fixed;
    z-index: 1;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    overflow: auto;
    background-color: rgba(0, 0, 0, 0.5);
}

.modal-content {
    display: flex;
    flex-direction: column;
    align-items: center;
    background-color: #fefefe;
    margin: 15% auto;
    padding: 20px;
    border: 1px solid #888;
    width: 50%;
    
}

label, input, select {
    margin-bottom: 15px;
}

.close {
    color: #aaa;
    float: right;
    font-size: 28px;
    font-weight: bold;
    cursor: pointer;
}

.close:hover,
.close:focus {
    color: black;
    text-decoration: none;
    cursor: pointer;
}

#player-form {
    text-align: center;
}
.player-info {
    display: flex;
    align-items: center;
    margin-top: 10px;
    
}

.color-options {
    display: flex;
    margin-right: 10px; /* Abstand zwischen dem Textfeld und den Farboptionen */
}

.color-option {
    width: 30px;
    height: 30px;
    border-radius: 50%;
    cursor: pointer;
    margin-left: 10px;
}

.color-option.yellow {
    background-color: yellow;
}

.color-option.green {
    background-color: green;
}

.color-option.blue {
    background-color: blue;
}

.color-option.red {
    background-color: red;
}

.color-option:hover {
    border: 2px solid #000;
}

#color1, #color2, #color3, #color4 {
    display: none; /* Verstecke die Eingabefelder für die Farbauswahl */
}

.informationBoard {
    padding-left: 5px;
    padding-top: 5px;
    width: 220px;
    height: 30px;
    font-size: 16px;
    color: #fafafa;
}

.text-container {
    margin-top: 7px;
    background-color: #3a503b;
    display: inline-block;
}

</style>