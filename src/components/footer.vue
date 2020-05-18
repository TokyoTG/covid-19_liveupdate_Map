<template>
  <div id="footer">
    <p id="copyRight">&copy; 2020 Toyeeb Ganiu</p>
    <ul>
      <li>
        <a href="https://web.facebook.com/tboynoni.toyeeb" target="_blank">
          <i class="fab fa-facebook"></i>
        </a>
      </li>
      <li>
        <a href="https://twitter.com/TG_Tokyo" target="_blank">
          <i class="fab fa-twitter"></i>
        </a>
      </li>
      <li>
        <a href="https://github.com/TokyoTG/" target="_blank">
          <i class="fab fa-github"></i>
        </a>
      </li>
      <li>
        <a href="tel:+2347069732084" target="_blank">
          <i class="fas fa-phone"></i>
        </a>
      </li>
      <li>
        <a href="mailto:ganiu.toyeeb.a@gmail.com">
          <i class="fas fa-envelope"></i>
        </a>
      </li>
    </ul>
    <p id="top">
      <a href="#nav">Back to top</a>
    </p>
  </div>
</template>
<style lang="css" scoped>
* {
  padding: 0;
  margin: 0;
}
#footer ul li {
  display: inline;
}
#footer ul {
  text-align: right;
}
#footer li a {
  display: inline-block;
  padding: 10px;
  background: white;
  border-radius: 100%;
  font-size: 0.9em;
  transition: 0.5s;
  margin-right: 10px;
  text-decoration: none;
  color: #1b5e20;
}
#footer li a:hover,
#top a:hover {
  font-weight: bold;
  background: #81c784;
  color: white;
}
#footer {
  margin-top: 15px;
  padding: 10px 15px;
  grid-template-columns: 30% 70%;
  display: grid;
  background: #455a64;
}
#copyRight {
  color: white;
  padding: 5px;
  font-size: 0.8em;
}

#top a {
  color: #1b5e20;
  padding: 5px 10px;
  margin-bottom: 10px;
  background: white;
  border-radius: 10px;
  text-decoration: none;
}
</style>