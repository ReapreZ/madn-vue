<template>
    <body>
        <br><br><br>
        <div id="player-popup" class="modal">
            <div class="modal-content">
                <span class="close" @click="closePopup">&times;</span>
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
                
        
                    <button type="button" id="startButton" @click="startGame">Spiel starten</button>
                </form>
            </div>
        </div>
        

        <div id="game-board"></div>
    </body>

</template>

<script>
import axios from 'axios';
export default {
    data() {
        return {
        playerPopupVisible: true,
        player1Name: '',
        player2Name: '',
        player3Name: '',
        player4Name: '',
        SOCKET_OPEN: false,
        SOCKET_INVERVALL: (0,5 * 1000),
        fieldList: [ [0, 4],[1, 4],[2, 4],[3, 4],[4, 4],[4, 3],[4, 2],[4, 1],[4, 0],[5, 0],[6, 0],[6, 1],[6, 2],[6, 3],[6, 4],[7, 4],[8, 4],[9, 4],[10, 4],[10, 5],[10, 6],[9, 6],[8, 6],[7, 6],[6, 6],[6, 7],[6, 8],[6, 9],[6, 10],[5, 10],[4, 10],[4, 9],[4, 8],[4, 7],[4, 6],[3, 6],[2, 6],[1, 6],[0, 6],[0, 5],],
        houseList: [ [0 ,0],[1 ,0],[0 ,1],[1 ,1], [9 ,0],[10 ,0],[9 ,1],[10 ,1], [0 ,9],[1 ,9],[0 ,10],[1 ,10], [9 ,9],[10 ,9],[9 ,10],[10 ,10],],
        pieceList: [ [0 ,0],[1 ,0],[0 ,1],[1 ,1], [9 ,0],[10 ,0],[9 ,1],[10 ,1], [0 ,9],[1 ,9],[0 ,10],[1 ,10], [9 ,9],[10 ,9],[9 ,10],[10 ,10],],
        goalList: [ [1 ,5],[2 ,5],[3 ,5],[4 ,5], [5 ,1],[5 ,2],[5 ,3],[5 ,4], [5 ,9],[5 ,8],[5 ,7],[5 ,6], [9 ,5],[8 ,5],[7 ,5],[6 ,5],],
        piecesOut: [0, 0, 0, 0],
        startingTileList: [ [0, 4], [6, 0], [4, 10], [10, 6]],
        playerColors: ['yellow', 'green', 'blue', 'red'],
        playerturn: 0,
        rolledDice: 0,
        playeramount: 0,
        timesPlayerRolled: 0,
        API_BASE_URL: "http://localhost:9000",
        gameBoard: '',
        startButton: ''
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

  adjustGameBoard() {
    if (this.playeramount < 2 || this.playeramount > 4) {
      console.error("Ungültige Spieleranzahl. Die Spieleranzahl muss zwischen 2 und 4 Spielern liegen.")
    }
    const fieldsToDelete = (4 - this.playeramount) * 4;
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
    this.gameBoard.parentElement.appendChild(gameInterface);
    this.adjustGameBoard();
    this.addStartPlayerCircles(this.houseList);
    await this.setTimesPlayerRolledInBackend(0);
  },

  async addStartPlayerCircles(list) {
    for (let i = 0; i < list.length; i++) {
      const element = list[i];
      const firstValue = element[0];
      const secondValue = element[1];
      const cell = this.findCellByRowAndColumn(firstValue, secondValue);
      await this.getPlayerTurnFromBackend();
      if (i % 4 === 0) {
        if (i !== 0) {
          await this.updatePlayerturn();
        }
      }
      await this.getPlayerTurnFromBackend();
      this.addPlayerCircle(cell, this.playerColors[this.playerturn]);
    }

    await this.setPlayerTurnInBackend(0);
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

  updateCoordinateInList(x, y, newX, newY) {
    for (let i = 0; i < this.pieceList.length; i++) {
      const coordinates = this.pieceList[i];
      const pieceX = coordinates[0];
      const pieceY = coordinates[1];

      if (pieceX === x && pieceY === y) {
        this.pieceList[i] = [newX, newY];
        return;
      }
    }
    console.error('Coordinates not found');
  },

  updateCoordinatesInListByIdx(list, newx, newY) {
    list[i] = [newX, newY];
  },

  findIndexFromList(list, x, y) {
    for (let i = 0; i < list.length; i++) {
      const coordinates = list[i];
      const pieceX = coordinates[0];
      const pieceY = coordinates[1];

      if (pieceX === x && pieceY === y) {
        return i;
      }
    }
  },

  async isMovingPieceOutAllowed(cell) {
    await this.getPlayerTurnFromBackend();
    if (this.rolledDice !== 6) {
      return;
    }
    const startingTileX = this.startingTileList[this.playerturn][0];
    const startingTileY = this.startingTileList[this.playerturn][1];
    const startingTile = this.findCellByRowAndColumn(startingTileX, startingTileY);
    if (cell.classList.contains('housep1') && this.playerturn === 0) {
      this.movePieceOut(cell, startingTile, startingTileX, startingTileY);
    }
    if (cell.classList.contains('housep2') && this.playerturn === 1) {
      this.movePieceOut(cell, startingTile, startingTileX, startingTileY);
    }
    if (cell.classList.contains('housep3') && this.playerturn === 2) {
      this.movePieceOut(cell, startingTile, startingTileX, startingTileY);
    }
    if (cell.classList.contains('housep4') && this.playerturn === 3) {
      this.movePieceOut(cell, startingTile, startingTileX, startingTileY);
    }
  },

  async movePieceOut(cell, startingTile, startingTileX, startingTileY) {
    await this.getPiecesListFromBackend();
    await this.getPlayerTurnFromBackend();
    await this.sleep(300);
    this.removePlayerCircle(cell);
    this.addPlayerCircle(startingTile, this.playerColors[this.playerturn]);
    const cellCoordinates = this.getCoordinatesFromCell(cell);
    const cellX = cellCoordinates[0];
    const cellY = cellCoordinates[1];
    this.updateCoordinateInList(cellX, cellY, startingTileX, startingTileY);
    await this.setPiecesListInBackend(this.pieceList);
  },

  async movePiece(cell) {
    await this.getPlayerTurnFromBackend();
    await this.getPiecesListFromBackend();
    await this.sleep(300);
    if (cell.classList.contains('housep1') || cell.classList.contains('housep2') || cell.classList.contains('housep3') || cell.classList.contains('housep4')) {
      return false;
    }
    const pieceColor = this.getColorAtCell(cell);
    const cellCoordinates = this.getCoordinatesFromCell(cell);
    const cellX = cellCoordinates[0];
    const cellY = cellCoordinates[1];
    const piecePosIdx = this.findIndexFromList(this.pieceList, cellX, cellY);
    const currentPlayerColor = this.playerColors[this.playerturn];
    if (pieceColor === currentPlayerColor) {
      const newPos = this.updateToNewPosition(piecePosIdx, cellX, cellY);
      await this.setPiecesListInBackend(this.pieceList);
      await this.sleep(300);
      const newPosX = newPos[0];
      const newPosY = newPos[1];
      this.removePlayerCircle(cell);
      this.addPlayerCircle(this.findCellByRowAndColumn(newPosX, newPosY), currentPlayerColor);

      if (this.rolledDice !== 6) {
        await this.updatePlayerturn();
      }
    }
  },

  async updatePlayerturn() {
    await this.sleep(100);
    await this.getPlayerTurnFromBackend();
    
    if (this.playerturn === this.playeramount - 1) {
      await this.setPlayerTurnInBackend(0);
    } else {
      console.log("this.playerturn= " + this.playerturn)
      await this.setPlayerTurnInBackend(this.playerturn + 1);
    }

    await this.sleep(100);
  },

  updateToNewPosition(piecePosIdx, cellX, cellY) {
    this.getRolledDiceFromBackend();
    const fieldIdx = this.findIndexFromList(this.fieldList, cellX, cellY);
    const newPos = this.fieldList[fieldIdx + this.rolledDice];
    this.pieceList[piecePosIdx] = newPos;
    return newPos;
  },

  getColorAtCell(cell) {
    const playerCircle = cell.querySelector('.player-circle');
    if (playerCircle) {
      return playerCircle.style.backgroundColor;
    }
    return null;
  },

  async isOnePieceOut() {
    await this.getPiecesListFromBackend();
    await this.getPlayerTurnFromBackend();
    await this.sleep(300);
    const playerIdx = this.playerturn * 4;
    for (let i = playerIdx; i < playerIdx + 4; i++) {
      const piecePosX = this.pieceList[i][0];
      const piecePosY = this.pieceList[i][1];
      const housePosX = this.houseList[i][0];
      const housePosY = this.houseList[i][1];
      if (!(piecePosX === housePosX && piecePosY === housePosY)) {
        return true;
      }
    }
    return false;
  },

  changeTextFieldText(newText) {
    const textField = document.querySelector('.informationBoard');
    if (textField) {
      textField.textContent = newText;
    } else {
      console.error('Text field not found.');
    }
  },

  createDice() {
    const dice = document.createElement('div');
    dice.className = 'dice';
    const diceImage = document.createElement('img');
    diceImage.src = 'src/assets/images/one.png';
    diceImage.className = 'dice-image';

    dice.appendChild(diceImage);

    dice.addEventListener('click', function () {
      this.rollDice();
    }.bind(this));

    return dice;
  },

  createMagicDice() {
    const dice = document.createElement('div');
    dice.className = 'dice';
    const diceImage = document.createElement('img');
    diceImage.src = 'src/assets/images/magicDice.png';
    diceImage.className = 'dice-image';

    dice.appendChild(diceImage);

    dice.addEventListener('click', function () {
      this.rollMagicDice();
    }.bind(this));

    return dice;
  },

  async rollMagicDice() {
    await this.getPlayerTurnFromBackend();
    this.rolledDice = 6;
    await this.setRolledDiceInBackend(6);
    this.setTimesPlayerRolledInBackend(0);
    this.changeTextFieldText("Player " + (this.playerturn + 1) + " rolled a 6. It's your turn again!");
  },

  kickOtherPieceOut(cell) {
    this.getPlayerTurnFromBackend();
    //check if x and y are already occupied
    if (!(cell.querySelector('.player-circle') !== null)) {
      const color = this.getColorAtCell(cell);
      //if so, check if it is a different color than self
      if (color !== this.playerColors[this.playerturn]) {
        this.findEmptyHouseSlot();
      } else {
        //do nothing
      }
    }
    //if it is a different color, find an empty house by giving the playerturn of the color as a parameter
    //move the piece back to their house
  },

  async findEmptyHouseSlot(kickedOutPlayer) {
    await this.getPiecesListFromBackend();
    await this.sleep(300);
    const playerIdx = kickedOutPlayer * 4;
    for (let i = playerIdx; i < playerIdx + 4; i++) {
      const piecePosX = this.pieceList[i][0];
      const piecePosY = this.pieceList[i][1];
      const housePosX = this.houseList[i][0];
      const housePosY = this.houseList[i][1];
      if (!(piecePosX === housePosX && piecePosY === housePosY)) {
        return [housePosX, housePosY];
      }
    }
    console.error("No free house found");
    return null;
  },

  async rollDice() {
    await this.getPiecesListFromBackend();
    await this.getPlayerTurnFromBackend();
    await this.getTimesPlayerRolledFromBackend();
    await this.sleep(300);
    const randomNumber = Math.floor(Math.random() * 6) + 1;
    const diceImage = document.querySelector('.dice-image');
    await this.setRolledDiceInBackend(randomNumber);
    this.rolledDice = randomNumber;
    switch (randomNumber) {
      case 1:
        diceImage.src = 'src/assets/images/one.png';
        break;
      case 2:
        diceImage.src = 'src/assets/images/two.png';
        break;
      case 3:
        diceImage.src = 'src/assets/images/three.png';
        break;
      case 4:
        diceImage.src = 'src/assets/images/four.png';
        break;
      case 5:
        diceImage.src = 'src/assets/images/five.png';
        break;
      case 6:
        diceImage.src = 'src/assets/images/six.png';
        break;
      default:
        console.error('Invalid random number.');
        break;
    }
    if (this.rolledDice === 6 && await this.isOnePieceOut()) {
      this.setTimesPlayerRolledInBackend(0);
      this.changeTextFieldText("Player " + (this.playerturn + 1) + " rolled a " + this.rolledDice + ". Choose a Piece to get out or move a Piece on the board. It's your turn again!");
      return;
    } else if (this.rolledDice === 6) {
      this.setTimesPlayerRolledInBackend(0);
      this.changeTextFieldText("Player " + (this.playerturn + 1) + " rolled a " + this.rolledDice + ". Choose a Piece to get out. It's your turn again!");
      return;
    } else if (await this.isOnePieceOut()) {
      this.setTimesPlayerRolledInBackend(0);
      this.changeTextFieldText("Player " + (this.playerturn + 1) + " rolled a " + this.rolledDice + ". Choose the Piece to move. It's Player " + this.getNextPlayerturn() + "'s turn next!");
      //this.updatePlayerturn();
    } else if (this.timesPlayerRolled !== 2) {
      this.setTimesPlayerRolledInBackend(this.timesPlayerRolled + 1);
      this.changeTextFieldText("Player " + (this.playerturn + 1) + " rolled a " + this.rolledDice + ". It's your turn again!");
    } else {
      this.setTimesPlayerRolledInBackend(0);
      this.changeTextFieldText("Player " + (this.playerturn + 1) + " rolled a " + this.rolledDice + ". It's Player " + this.getNextPlayerturn() + "'s turn!");
      await this.updatePlayerturn();
    }
  },

  getNextPlayerturn() {
    this.getPlayerTurnFromBackend();
    if (this.playerturn === this.playeramount - 1) {
      return "1";
    } else {
      if (this.playeramount === 1) {
        return 1;
      } else {
        return (this.playerturn + 2);
      }
    }
  },

  createRow(rowData, rowIndex) {
    const row = document.createElement('div');
    row.className = 'game-row';

    const maxHeight = Math.max(...rowData.map(cellData => cellData.className === 'empty' ? 0 : 1));

    rowData.forEach((cellData, columnIndex) => {
      const cell = this.createCell(cellData.className, rowIndex, columnIndex);

      cell.style.height = maxHeight > 0 ? '35px' : 'auto';

      cell.addEventListener('click', function () {
        // ...
      });

      row.appendChild(cell);
    });

    return row;
  },

  isPlayerCirclePlaceable(cell) {
    if (cell.classList.contains('empty')) {
      return false;
    }

    return !(cell.querySelector('.player-circle') !== null);
  },

  findCellByRowAndColumn(row, column) {
    const targetRow = this.gameBoard.children[row];

    if (targetRow) {
      const targetCell = targetRow.children[column];

      if (targetCell) {
        return targetCell;
      } else {
        console.error('Cell not found.');
        return null;
      }
    } else {
      console.error('Cell not found.');
      return null;
    }
  },

  getCoordinatesFromCell(cell) {
    const row = parseInt(cell.getAttribute('data-row'));
    const column = parseInt(cell.getAttribute('data-column'));
    return [row, column];
  },

  createPlayerCircle(color) {
    const playerCircle = document.createElement('div');
    playerCircle.className = 'player-circle';
    playerCircle.style.backgroundColor = color;
    return playerCircle;
  },

  addPlayerCircle(cell, color) {
    if (this.isPlayerCirclePlaceable(cell)) {
      const playerCircle = this.createPlayerCircle(color);
      cell.appendChild(playerCircle);
    }
  },

  removePlayerCircle(cell) {
    if (!this.isPlayerCirclePlaceable(cell)) {
      const playerCircle = cell.querySelector('.player-circle');

      if (playerCircle) {
        cell.removeChild(playerCircle);
      }
    }
  },

  logList(list) {
    for (let i = 0; i < list.length; i++) {
      console.log(`Item ${i + 1}: [${list[i].join(', ')}]`);
    }
  },

  createGameBoard() {
    const rowsData = [[
                { className: 'housep1' },{ className: 'housep1' },{ className: 'empty' },{ className: 'empty' },{ className: 'goalp1'},{ className: 'tile' },{ className: 'tile' },{ className: 'empty' },{ className: 'empty' },{ className: 'housep3' },{ className: 'housep3' },
            ],
            [
                { className: 'housep1' },{ className: 'housep1' },{ className: 'empty' },{ className: 'empty' },{ className: 'tile' },{ className: 'goalp1' },{ className: 'tile' },{ className: 'empty' },{ className: 'empty' },{ className: 'housep3' },{ className: 'housep3' },
            ],
            [
                { className: 'empty' },{ className: 'empty' },{ className: 'empty' },{ className: 'empty' },{ className: 'tile' },{ className: 'goalp1' },{ className: 'tile' },{ className: 'empty' },{ className: 'empty' },{ className: 'empty' },{ className: 'empty' },
            ],
            [
                { className: 'empty' },{ className: 'empty' },{ className: 'empty' },{ className: 'empty' },{ className: 'tile' },{ className: 'goalp1' },{ className: 'tile' },{ className: 'empty' },{ className: 'empty' },{ className: 'empty' },{ className: 'empty' },
            ],
            [
                { className: 'tile' },{ className: 'tile' },{ className: 'tile' },{ className: 'tile' },{ className: 'tile' },{ className: 'goalp1' },{ className: 'tile' },{ className: 'tile' },{ className: 'tile' },{ className: 'tile' },{ className: 'goalp3' },
            ],
            [
                { className: 'tile' },{ className: 'goalp2' },{ className: 'goalp2' },{ className: 'goalp2' },{ className: 'goalp2' },{ className: 'empty' },{ className: 'housep3' },{ className: 'housep3' },{ className: 'housep3' },{ className: 'housep3' },{ className: 'tile' },
            ],
            [
                { className: 'goalp2' },{ className: 'tile' },{ className: 'tile' },{ className: 'tile' },{ className: 'tile' },{ className: 'goalp4' },{ className: 'tile' },{ className: 'tile' },{ className: 'tile' },{ className: 'tile' },{ className: 'tile' },
            ],
            [
                { className: 'empty' },{ className: 'empty' },{ className: 'empty' },{ className: 'empty' },{ className: 'tile' },{ className: 'goalp4' },{ className: 'tile' },{ className: 'empty' },{ className: 'empty' },{ className: 'empty' },{ className: 'empty' },
            ],
            [
                { className: 'empty' },{ className: 'empty' },{ className: 'empty' },{ className: 'empty' },{ className: 'tile' },{ className: 'goalp4' },{ className: 'tile' },{ className: 'empty' },{ className: 'empty' },{ className: 'empty' },{ className: 'empty' },
            ],
            [
                { className: 'housep2' },{ className: 'housep2' },{ className: 'empty' },{ className: 'empty' },{ className: 'tile' },{ className: 'goalp4' },{ className: 'tile' },{ className: 'empty' },{ className: 'empty' },{ className: 'housep4' },{ className: 'housep4' },
            ],
            [
                { className: 'housep2' },{ className: 'housep2' },{ className: 'empty' },{ className: 'empty' },{ className: 'tile' },{ className: 'tile' },{ className: 'goalp4' },{ className: 'empty' },{ className: 'empty' },{ className: 'housep4' },{ className: 'housep4' },
            ],
        ];
    rowsData.forEach((rowData, rowIndex) => {
      this.gameBoard.appendChild(this.createRow(rowData, rowIndex));
    });
  },

  async getPlayerTurnFromBackend() {
    try {
        const response = await axios.get('http://localhost:9000/getPlayerturn');
        this.playerturn = response.data;
        console.log(response.data + " getPlayerturn");
      } catch (error) {
        console.error('Error:', error);
      }
    },

  async getRolledDiceFromBackend() {
    try {
        const response = await axios.get('http://localhost:9000/getRolledDice');
        this.rolledDice = response.data;
      } catch (error) {
        console.error('Error:', error);
      }
    },

  async getPlayeramountFromBackend() {
    try {
        const response = await axios.get('http://localhost:9000/getPlayeramount');
        this.playeramount = response.data;
      } catch (error) {
        console.error('Error:', error);
      }
    },

  async getTimesPlayerRolledFromBackend() {
    try {
        const response = await axios.get('http://localhost:9000/getTimesPlayerRolled');
        this.timesPlayerRolled = response.data;
      } catch (error) {
        console.error('Error:', error);
      }
    },

  async getPiecesOutFromBackend() {
    try {
        const response = await axios.get('http://localhost:9000/getPiecesOut');
        this.piecesOut = response.data;
      } catch (error) {
        console.error('Error:', error);
      }
    },

    async getPiecesListFromBackend() {
      try {
        const response = await axios.get('http://localhost:9000/getPiecesList');
        this.piecesList = response.data;  // Assuming the playerturn is returned in the response
      } catch (error) {
        console.error('Error:', error);
      }
    },

    async setPlayerTurnInBackend(playerturnBackend) {
  try {
    console.log('this.playerturn vor dem Aufruf:', this.playerturn);
    console.log('playerturnBackend vor dem Aufruf:', playerturnBackend);

    const response = await axios.post('http://localhost:9000/setPlayerturn', {
      playerturnBackend
    });

    console.log('this.playerturn nach dem Aufruf:', this.playerturn);
    console.log('playerturnBackend nach dem Aufruf:', playerturnBackend);

    console.log('Erfolgreich an das Backend gesendet:', response.data);
  } catch (error) {
    console.error('Fehler beim Senden an das Backend:', error);
  }
},


  async setRolledDiceInBackend(rolledDiceBackend) {
    $.ajax({
      type: "POST",
      url: this.API_BASE_URL + "/setRolledDice",
      contentType: "application/json",
      data: JSON.stringify({ rolledDiceBackend }),
      success: function (response) {
        console.log('Erfolgreich an das Backend gesendet:', response);
      },
      error: function (error) {
        console.error('Fehler beim Senden an das Backend:', error);
      }
    });
  },

  async setPlayeramountInBackend(playeramountBackend) {
    try {
        const response = await axios.get('http://localhost:9000/getPlayeramount');
        this.playeramount = response.data;
      } catch (error) {
        console.error('Error:', error);
      }
    },

  setTimesPlayerRolledInBackend(timesPlayerRolledBackend) {
    $.ajax({
      type: "POST",
      url: this.API_BASE_URL + "/setTimesPlayerRolled",
      contentType: "application/json",
      data: JSON.stringify({ timesPlayerRolledBackend }),
      success: function (response) {
        console.log('Erfolgreich an das Backend gesendet:', response);
      },
      error: function (error) {
        console.error('Fehler beim Senden an das Backend:', error);
      }
    });
  },

  setPiecesOutInBackend(pieceOutBackend) {
    $.ajax({
      type: "POST",
      url: this.API_BASE_URL + "/setPiecesOut",
      contentType: "application/json",
      data: JSON.stringify({ piecesOutBackend }),
      success: function (response) {
        console.log('Erfolgreich an das Backend gesendet:', response);
      },
      error: function (error) {
        console.error('Fehler beim Senden an das Backend:', error);
      }
    });
  },

  async setPiecesListInBackend(piecesListBackend) {
    $.ajax({
      type: "POST",
      url: this.API_BASE_URL + "/setPiecesList",
      contentType: "application/json",
      data: JSON.stringify({ piecesListBackend }),
      success: function (response) {
        console.log('Erfolgreich an das Backend gesendet:', response);
      },
      error: function (error) {
        console.error('Fehler beim Senden an das Backend:', error);
      }
    });
  },

  createGameInterface() {
    const interfaceContainer = document.createElement('div');
    interfaceContainer.className = 'game-interface';

    const dice = this.createDice();
    const magicDice = this.createMagicDice();

    const textContainer = document.createElement('div');
    textContainer.className = 'text-container';
    const textField = document.createElement('div');
    textField.className = 'informationBoard';
    textField.textContent = 'Roll the dice to begin playing';
    textContainer.append(textField);

    interfaceContainer.appendChild(dice);
    interfaceContainer.appendChild(magicDice);
    interfaceContainer.appendChild(textContainer);

    return interfaceContainer;
  }

},
mounted() {
   this.gameBoard = document.getElementById('game-board');
   this.startButton = document.getElementById('startButton');

  this.openPopup();
},
};
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
    margin-right: 10px;
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
    display: none;
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