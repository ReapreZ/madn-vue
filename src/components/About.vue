<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-12 text-center mb-4" style="margin-top: 110px;">
        <button class="btn btn-light" type="button" data-bs-toggle="collapse" data-bs-target="#collapseWidthExample" aria-expanded="false" aria-controls="collapseWidthExample" >
          <h1 class="about-header">About Us</h1>
        </button>
      </div>

      <div class="col-12">
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
@import '../assets/styles.css';
h1.about-header {
  font-family: 'Poppins';
  font-size: 48px;
  color: red;
}

p.about {
  display:flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  min-height: 30vh;
  font-weight: 500;
  font-size: 18px;
}

p.github-logo {
  display: flex;
  justify-content: center;
  align-items: center;
}

a.about-us {
  min-height: 5vh;
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
