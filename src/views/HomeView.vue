<template>
  <div>
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
      <div class="container-fluid">
        <router-link class="navbar-brand" to="/">
          <img
            src="@/assets/orange-logo.svg"
            width="50"
            height="50"
            alt="Boosted - Back to Home"
            loading="lazy"
          />
        </router-link>
        <button
          class="navbar-toggler"
          type="button"
          data-bs-toggle="collapse"
          data-bs-target="#navbarNav"
          aria-controls="navbarNav"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
          <ul class="navbar-nav ms-auto">
            <li class="nav-item">
              <h1 class="mt-0">Cloud Assessment</h1>
            </li>
          </ul>
        </div>
      </div>
    </nav>
    <section class="mt-3">
      <div class="forms my-4">
        <div class="container">
          <div class="row">
            <div class="col-md-4">
              <h3 class="text-primary">Welcome back</h3>

              <form @submit="login">
                <input
                  type="hidden"
                  id="token"
                  name="csrf_app"
                  value="d31ab597f09de740ac84097b48729f0e"
                  
                />

                <div class="form-group">
                  <label for="exampleFormControlInput1" >E-Mail Address </label>
                  <input
                    type="text"
                    name="userName"
                    
                    class="input form-control"
                   
                    v-model="email"
                    
                  />
                </div>
                <div class="form-group">
                  <label for="exampleFormControlInput2">Password</label>
                  <input
                    type="password"
                    name="password"
                    class="input form-control"
                    v-model="password"
                  />
                </div>
                
<div class="alert alert-warning" role="alert" v-if="message" >
  <span class="alert-icon"><span class="visually-hidden">Success</span></span>
  <p>{{message}}</p>
</div>


                <br />
                <button type="submit" class="btn loginButtonSubmit btn-primary">Login</button>
                
              </form>
            </div>

            <div class="col-md-6 offset-md-2">
              <h3 class="text-primary">First visit?</h3>
              <h4>
                Simply complete a short registration form to create an account and get access to
                this site.
              </h4>
              <a href="#" class="btn btn-secondary">Register</a>
            </div>
          </div>
        </div>
      </div>
    </section>
    <Footer class="footer"></Footer>
  </div>
</template>

<script>
//import MyButton from '@/components/MyButton.vue'
import Footer from '../components/Footer.vue'
import axios from 'axios';
export default {
  data() {
    return {
      email: '',
      password: '',
      message: ''
    }
  },
  components: {
    //MyButton,
    Footer
  },
  methods: {
      login(event) {
        event.preventDefault();
        this.message = '';

      if (!this.email || !this.password) {
        this.message = 'Please enter both your email and password.';
        return;
      }
  
        axios.post('http://localhost:8080/api/v1/auth/authenticate', {
          email: this.email,
          password: this.password
        })
        .then(response => {
          const token = response.data.token;
  
          // Store the token in localStorage
          localStorage.setItem('token', token);
  
          // Set authorization header for all Axios requests
          axios.defaults.headers.common['Authorization'] = `Bearer ${token}`;
  
          // Redirect to '/applications' route
          this.$router.push('/applications');
          
        })
        .catch(error => {
          if (error.response && error.response.status === 403) {
          this.message = 'Incorrect username or password.';
        } else {
          this.message = 'Login failed. Please try again later.';
        }
        

    
        console.error('Login failed:', error);
      });
      }
    }
  };
</script>

<style scoped>
.footer {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: #333;
  color: #fff;
  padding: 10px;

  display: flex;
  flex-direction: column;
}
.content {
  margin-bottom: 60px; /* Set a margin to prevent content from being hidden behind the footer */
}
</style>


