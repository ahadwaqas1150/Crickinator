<template>
  <div class="container">
    <div class="overlay">
    <div class="insidebox">
      <img src="../assets/hello.jpg" class="pfp">
      <div class="smallercontainer">
        <v-responsive>
          <v-text-field
            class="username"
            label="Username"
            color="#255b5e"
            variant="outlined"
            :error-messages="usernameError"
            :rules="[required]"

            v-model="username"
          ></v-text-field>
        </v-responsive>    

        <v-responsive>
          <v-text-field
            class="password"
            label="Password"
            color="#FF4500"
            variant="outlined"
            v-model="password"
            :error-messages="passwordError"
            :rules="[required]"
            :append-inner-icon="showpassword ? 'mdi-eye' : 'mdi-eye-off'"
            :type="showpassword ? 'text' : 'password'"
            @click:append-inner="togglePassword"
          ></v-text-field>
        </v-responsive>    

        <v-btn class="signup" @click="handleClick()" type="submit">
          <template v-slot:prepend>
            <CricketBatIcon />
          </template>
          Login
        </v-btn>
        
        <p class="text">
             Don't have an Account? 
            <router-link to="/signup" class="login-link">Signup</router-link>
          </p>
      </div>
      <div class="copyright">Copyright © 2024 Crickinator</div>
    </div>
  </div>
  </div>
</template>

<script >
import { useRouter } from 'vue-router';
import CricketBatIcon from '../components/CricketBat-Icon.vue';
import { ref } from 'vue';
import {API,setUserName} from '../main';
export default {
  components: {
    CricketBatIcon
  },
  setup() {
    const router = useRouter();
    const username = ref('');
    const password = ref('');
    const message = ref('');
    const usernameError = ref('');
    const passwordError = ref('');
    const showpassword = ref(false);
    const handleClick =  () => {
      console.log("Login clicked");
      console.log(username.value, password.value);

      // Validate the username and password from the database
      const requestNews = async() => {
      try {
        const request = await fetch(API+"/verify/user", {
          method:"POST",
          headers: {
            "Content-Type": "application/json"
          },
          body: JSON.stringify({
            username: username.value,
            password: password.value
          })
        }
        );
        const response = await request.json();
          console.log(response)
        if(response["status"] == "ok") {
          console.log(response)
          setUserName(username.value);
          console.log("Login successful");
          router.push('/welcome');
        }
        else {
          if(response.message == "User not found") {
            usernameError.value = "Username not found";
            passwordError.value = "";
          }
          else if(response.message == "Invalid password") {
            passwordError.value = "Password incorrect";
            usernameError.value = "";
          }
          throw new Exception("Failed to POST")
        }
      }
      catch(e) {
        console.log(e);
      }
    }
    requestNews();
    console.log("This page is loaded")
    };
    const togglePassword = () => {
      showpassword.value = !showpassword.value;
      setTimeout(() => {
        password.value.focus();
        password.value.selectionStart = password.value.value.length;
      }, 0);
    };
  return {
    router, username, password, handleClick, message, usernameError, passwordError,
    showpassword,togglePassword
  }
  },
  }

</script>

<style scoped>
.container {
  width: 100%;
  height: 100vh; /* Set height to viewport height */
  background-image: url('../assets/stadium.webp');
  background-size: cover;
  display: flex;
  justify-content: flex-end; /* Center horizontally */
  align-items: center; /* Center vertically */
  background-color: #d64e4a;
  z-index: -1;
}

.overlay {
  width: 100%;
  height: 100vh;
  background-image: linear-gradient(to bottom, #292929c0,#292929c0,#292929c0);
  display: flex;
  justify-content: flex-end;
  align-items: center;
  z-index: 0;
}
.insidebox {
  width: 425px;
  height: auto;
  background-color: #303030;
  border-radius: 40px;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px; /* Added padding for better spacing */
  margin-right: 1rem;
  z-index: 1000;
}

.pfp {
  width: 150px;
  height: 150px;
  border-radius: 50%;
  margin-top: 20px; /* Adjusted margin for better alignment */
}

.username, .password {
  margin-top: 10px;
}

.smallercontainer {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 25px;
}

.v-text-field {
  width: 300px; /* Full width */
}

.signup {
  background-color: #247a65;
  color: #FFFFFF;
  margin-top: 10px;
  width: 300px;
}

.signup:hover {
  background-color: #2eb493;
  color: #FFFFFF;
}

.login-link {
  font-size: 13px;
  color: #247a65;
  margin-top: 10px;
  text-decoration: underline;
  cursor: pointer;
}
.t,.text{
    font-size: 13px;
  color: #247a65;
  margin-top: 10px;
}
.login-link:hover {
  color: #2eb493; /* Change color on hover if desired */
  text-decoration: underline;
}

.copyright {
  color: #247a65;
  margin-top: 30px;
}

</style>
