<template>
  <div id="app">
    <div id="nav">
      <h5>
        <img src="../public/Nigeria_flag_map.png" alt height="30" width="30" /> COVID-19 LIVEUPDATE
      </h5>
      <router-link to="/about">
        <i class="fas fa-list"></i>
        <span class="nav-text">About</span>
      </router-link>
      <router-link to="/">
        <i class="fas fa-home"></i>
        <span class="nav-text">Home</span>
      </router-link>
    </div>
    <router-view />
  </div>
</template>

<style lang="css">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin: 0;

  color: #2c3e50;
  /* color: white; */
}

#app .fas {
  font-size: 1.1em;
  margin-right: 5px;
}

#nav {
  padding: 10px 15px;
  margin: 0;
  padding: 0;
  text-align: left;

  overflow: hidden;
  background: #81c784;
  margin-bottom: 10px;
}
#nav a {
  font-weight: bold;
  margin-right: 10px;
  float: right;
  color: #1b5e20;
  /* margin-top: 10px; */
  text-decoration: none;
  display: block;
  padding: 20px 15px;
}
#nav h5 {
  font-size: 0.9em;
  box-sizing: border-box;
  display: inline-block;
  margin: 0;
  color: white;
  padding: 0;
  padding: 10px 15px;
}
#nav .router-link-exact-active {
  color: white;
  background: #455a64;
  /* text-decoration: underline; */
}
body {
  /* background: #455a64; */
  margin: 0;
  padding: 0;
}

@media only screen and (max-width: 650px) {
  .nav-text {
    display: none;
  }
  #app .fas {
    font-size: 1.3em;
  }
}
</style>
