<template>
  <div class="darkTheme loginPage">
    <h1 style="width: 100%;">Coding Journal</h1>

    <form @submit.prevent="onSubmitLogin">
        <!-- <input v-model="search" type="text" placeholder="Search for posters" required>
        <input type="submit" value="Search" class="btn"> -->
      <h2>Login</h2>
      <div class="imgcontainer">
        <img src="../assets/avatar-512.png" alt="Avatar" class="avatar">
      </div>

      <div class="container">
        <input v-model="username" type="text" placeholder="Enter Username" autocomplete="off" required><br>
        <input v-model="psw" type="password" placeholder="Enter Password" required><br>

        <button type="submit">Login</button>
      </div>
    </form>

    <form @submit.prevent="onSubmitSignup" autocomplete="off">
      <h2>Sign Up</h2>
        <div class="imgcontainer">
          <img src="../assets/avatar-512.png" alt="Avatar" class="avatar">
        </div>

        <div class="container">
          <input v-model="username" type="text" placeholder="Enter Username" required><br>
          <input v-model="email" type="email" placeholder="Enter Email" required><br>
          <input v-model="psw" type="password" placeholder="Enter Password" required><br>

          <button type="submit">Sign Up</button>
        </div>
      </form>

    <button onclick="location.href='http://www.jamesbartulis.com/';">Back</button>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: 'Home',
  data() {
    return {
    	username: "",
    	email: "",
    	psw: "",
    }
  },
  methods: {
  	async onSubmitLogin() {
      let base = "http://192.168.86.42:3000/";
      base += this.search;
      const response = await axios ({
        url: base,
        method: "GET"
      })
      this.results = response.data;
  	},
  	async onSubmitSignup() {
      let base = "http://192.168.86.42:3000/";
      base += this.search;
      const response = await axios ({
        url: base,
        method: "GET"
      })
      this.results = response.data;
  	},
  },
}
</script>

<style scoped>
	.imgcontainer {
	  text-align: center;
	  margin: 24px 0 12px 0;
	}
	img.avatar {
	  width: 20%;
	  border-radius: 50%;
	}
	.container {
	  background-color: #666; /* sign in page, inner box background */
	  padding: 16px;
	  margin: auto;
	  width: 40%;
	}
	.darkTheme {
	  --color-primary: #111; /* general background */
	  --color-secondary: #070707; /* left column background */
	  --color-button: #1A531D; /* generic button color */
	  --color-button-cancel: #f44336;
	  --color-input-text-border: #555;
	  --color-typesnip: inherit;
	  /*--color-typesnip: black;*/
	  --color-languagetag: inherit;
	  /*--color-languagetag: black;*/
	  --color-removesnip: #640000;
	  --color-copybtn: #000054;
	  --color-editbtn: #123814;
	  --color-filterbtn: #123814;
	  --color-textarea-border: #555;
	  --color-features: #a2a2a2;
	  --color-switch: #640000;
	  --color-text-background: #222;
	  --color-jumptotop: #AAA;
	  --color-usertable: #333;
	  --color-filterform: #222;
	  --color-select: #222;

	  --color-switch-color: #b1b1b1;
	  --font-color: #CCC; /* font color */
	}
	.loginPage{
	  display: flex;
	  margin: 0;
	  padding: 0;
	  text-align: center;
	  align-items: center;
	  background-color: #777;
	  flex-direction: horizontal;
	  flex-wrap: wrap;
	  height: 92vh;
	}
	.loginPage > form {
	  margin: 0;
	  background-color: inherit;
	  width: 50%;
	  text-align: center;
	}
	button {
	  background-color: var(--color-button); /* generic button color */
	  color: var(--font-color); /*#CCC*/
	  padding: 14px 20px;
	  margin: 8px 0;
	  border: none;
	  cursor: pointer;
	  width: 100%;
	}

	/* Add a hover effect for buttons */
	button:hover {
	  opacity: 0.8;
	}
	input[type=text], input[type=password], input[type=email] {
	  width: 100%;
	  padding: 12px 20px;
	  margin: 8px 0;
	  display: inline-block;
	  border: 1px solid #ccc; /* outline for inputs sign in */
	  box-sizing: border-box;
	}
</style>
