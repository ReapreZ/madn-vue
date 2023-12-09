<template>
    <main>
      <body class="index-body">
      <br><br><br><br>
      <div class="container mt-1">
  
      <h1 class="welcome" data-text="WELCOME TO MADN">WELCOME TO MADN</h1>
      <form action="index">
      <div class="madn-image">
        <br>
        <br>
          <img src="../assets/images/madnimage.png" alt="MADN" width="600" height="200">
      </div>
      </form>
      </div>
      </body>
    </main>
  </template>
  
  <style scoped>
  @import '../assets/styles.css';
  img {
      max-width: 100%;
      height: auto;
  }
  body.index-body
  {
      display:flex;
      justify-content: center;
      align-items: center;
      min-height: 50vh;
      background: white;
      padding-top: 10px;
      text-align: justify;
  }
  h1.welcome {
      padding-left: 20vh;
      font-family: 'Poppins', sans-serif;
      position: relative;
      font-size: 5vw;
      color: white;
      -webkit-text-stroke: 0.2vw grey;
      text-transform: uppercase;
      text-align: justify;
  }
  
  h1.welcome::before {
      padding-left: 20vh;
      white-space: nowrap;
      content: attr(data-text);
      position: absolute;
      top: 0;
      left: 0;
      height: 100%;
      text-align: justify;
      color: rgb(255, 0, 0);
      -webkit-text-stroke: 0vw grey;
      border-right: 2px solid rgb(0, 0, 0);
      overflow: hidden;
      animation: animate 6s linear infinite;
  }
  
  @media (max-width: 768px) {
      h1.welcome {
          font-size: 9vw; 
          -webkit-text-stroke: 0.1vw grey; 
      }
  
      h1.welcome::before {
          border-right: 1px solid rgb(0, 0, 0); 
      }
  }
      @keyframes animate
      {
          0%,10%,100%
          {
              width: 0%;
          }
          70%,90%
          {
              width: 100%;
          }
  }
  
  div.madn-image
  {
      text-align:center;
  }
  </style>
  