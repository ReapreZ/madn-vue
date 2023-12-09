<template>
    <div>
      <div class="container mt-1">
        <h1 class="feedback-header">Feedback</h1>
        <div class="feedback">
          <form @submit.prevent="submitFeedback">
            <label for="email" style="font-size: large;">E-Mail Adresse</label>
            <input v-model="email" type="text" id="email" name="mail" placeholder="Deine E-Mail Adresse..">
  
            <label for="feedback" style="font-size: large;">Feedback</label>
            <textarea v-model="feedback" id="feedback" name="fb" placeholder="Schreib etwas.." style="height:200px"></textarea>
  
            <button type="submit" class="btn btn-danger feedback-button" style="--clr:#ff3333">
              <span>Senden</span>
            </button>
          </form>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        email: '',
        feedback: '',
      };
    },
    methods: {
      submitFeedback() {
        // Add your logic to handle the form submission, for example, sending feedback to the server
        console.log('Email:', this.email);
        console.log('Feedback:', this.feedback);
        // You can make an API call or perform any other actions here
      },
    },
  };
  </script>
  
  <style scoped>
  h1.feedback-header {
    font-family: 'Poppins';
    font-size: 48px;
    text-align: center;
    color: red;
  }
  
  input[type="text"],
  select,
  textarea {
    width: 100%;
    padding: 12px;
    border: 5px solid #000000;
    border-radius: 4px;
    box-sizing: border-box;
    margin-top: 6px;
    margin-bottom: 16px;
    resize: vertical;
  }
  
  a.feedback-button {
    background: #000;
    color: #fff;
    text-decoration: none;
    font-size: 1.5em;
    letter-spacing: 0.1em;
    font-weight: 400;
    padding: 5px 20px;
    cursor: pointer;
    transition: 0.5s;
  }
  
  a.feedback-button:hover {
    background: var(--clr);
    letter-spacing: 0.25em;
    box-shadow: 0 0 35px var(--clr);
  }
  
  .feedback {
    margin: 10px;
    border-radius: 5px;
    border: 5px solid #000000;
    background-color: #f2f2f2;
    padding: 50px;
  }
  </style>
  