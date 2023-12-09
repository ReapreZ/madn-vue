<template>
    <div class="center-container">
      <h1 class="rules-header">Game Rules</h1>
      <p class="rules-text">
        • This game can be played with 1-4 Players.<br>
        • Each player has four pieces to move, and they all start at their home field.<br>
        • A piece can be moved on their own starting point if a player rolls a six.<br>
        • If all pieces are still in their home field, a player is allowed to roll the dice three times.<br>
        • If a six is rolled, the player is allowed to roll the dice again.<br>
        • When a piece is already on the starting point and a six is rolled, the player first has to move the piece that is on the starting point.<br>
        • The goal is to get all the four pieces into their finish fields.<br>
        • Pieces can jump over other pieces and throw out pieces from other players if they land on their field.<br>
        • A Player cannot throw out his own pieces and cannot advance further than the last field in the home row.
      </p>
    </div>
  </template>
  
  <script>
  export default {};
  </script>
  
  <style scoped>
  .center-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    margin-top: -10vh;
  }
  
  h1.rules-header {
    font-family: 'Poppins';
    font-size: 48px;
    color: red;
  }
  
  p.rules-text {
    font-family: 'Poppins';
    background-color: #f2f2f2;
    font-weight: 600;
    max-width: 1000px;
    border: 5px solid rgb(0, 0, 0);
    padding: 50px;
    margin: 10px;
    text-align: justify;
  }
  </style>
  