<script setup>
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
</script>

<template>
  <header class="head">
    <body class="main-body">
    <!-- NavBar -->
    <nav class="navbar navbar-expand-lg bg-body-tertiary fixed-top">
        <div class="container-fluid">
          <a class="navbar-brand" href="#">MADN</a>
          <button class="navbar-toggler" type="button" data-bs-toggle="offcanvas" data-bs-target="#offcanvasNavbar" aria-controls="offcanvasNavbar" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
          </button>
          <div class="offcanvas offcanvas-end" tabindex="-1" id="offcanvasNavbar" aria-labelledby="offcanvasNavbarLabel">
            <div class="offcanvas-header">
              <h5 class="offcanvas-title" id="offcanvasNavbarLabel">Navigate</h5>
              <button type="button" class="btn-close" data-bs-dismiss="offcanvas" aria-label="Close"></button>
            </div>
            <!-- NavbarContent -->
            <div class="offcanvas-body">
              <ul class="navbar-nav justify-content-end flex-grow-1 pe-3">
                <li class="nav-item">
                  <a class="nav-link active" aria-current="page" href="/">Home</a>
                  <!-- <a class="nav-link active @if(currentPath == "/") {active}" aria-current="page" href="@routes.HomeController.index()">Home</a> -->
                </li>
                <li class="nav-item">
                  <a class="nav-link active" aria-current="page" href="/game">Game</a>
                </li>
                <li class="nav-item">
                  <a class="nav-link active" aria-current="page" href="/rules">Rules</a>
                    <!-- <a class="nav-link active @if(currentPath == "/rules") {active}" aria-current="page" href="@routes.HomeController.rules">Rules</a> -->
                  </li>
                  <li class="nav-item">
                    <a class="nav-link active" aria-current="page" href="/feedback">Feedback</a>
                  </li>
                  <li class="nav-item">
                    <a class="nav-link active" aria-current="page" href="/about">About</a>
                  </li>
              </ul>
              
            </div>
          </div>
        </div>
        
      </nav>
      <section class="content"></section>
</body>
  </header>

  <RouterView />
</template>

<style scoped>

header.head{
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: 'Poppins',sans-serif;
}

body.main-body
{
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 70vh;
    
    background: #d4d4d4;
}
</style>
