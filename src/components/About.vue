<template>
    <div class="container-fluid">
      <div class="row align-items-center">
        <br><br><br><br><br>
  
        <p>
          <button class="btn btn-light" type="button" data-bs-toggle="collapse" data-bs-target="#collapseWidthExample" aria-expanded="false" aria-controls="collapseWidthExample">
            <h1 class="about-header text-center">About Us</h1>
          </button>
        </p>
        <div style="min-height: 120px;">
          <div class="collapse collapse-horizontal" id="collapseWidthExample">
            <div class="card card-body">
  
              <p class="about">
                This is a project made by Ümit Simsek and Can Kabak. <br>
                This was made for the lecture Web-Applications at the HTWG-Konstanz.
              </p>
  
              <p class="github-logo">
                <i class="fa fa-github" style="font-size:96px; color:red"></i>
              </p>
              <a class="about-us" href="https://github.com/ReapreZ/madn-webapp">Link to the github</a>
  
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'About',
  };
  </script>
  
  <style scoped>
  h1.about-header {
    font-family: 'Poppins';
    font-size: 48px;
    text-align: center;
    color: red;
  }
  
  p.about {
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    min-height: 20vh;
    font-weight: 500;
    font-size: 18px;
  }
  
  p.github-logo {
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  a.about-us {
    display: flex;
    justify-content: center;
    align-items: center;
    text-decoration: none;
    color: #0078d4;
    transition: color 0.3s;
  }
  
  a.about-us:hover {
    text-decoration: underline;
  }
  </style>
  