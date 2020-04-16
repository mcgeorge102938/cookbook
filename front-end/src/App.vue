<template>
<div id="app">
  <div class="header">
    <router-link to="/">
      <div class="logo">
        <h1>Home</h1>
        <router-link to="/"><img src="/cookbook.jpeg"></router-link>
      </div>
    </router-link>
    <div class="title">
      <h1>George Family</h1>
      <h2>COOKBOOK</h2>
    </div>
  </div>
  <div class="content">
    <router-view />
  </div>
  <div class="footer">
    <router-link to="/admin"><h1>Add A Recipe</h1></router-link>
    <router-link to="/admin2"><h1>Edit or Remove A Recipe</h1></router-link>
    <a href="https://github.com/mcgeorge102938/cookbook">Github</a>
  </div>
</div>
</template>

<style>
html {
  box-sizing: border-box;
}

.title h2 {
  font-size: 100px;
}

body {
  font-family: 'Montserrat', sans-serif;
  font-size: 16px;
  background: #fff;
  padding: 0px;
  margin: 0px;
}

.logo {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
  flex-direction: column;
}

.logo h1 {
  font-size: 35px;
  text-decoration: none;
  color: black;
}

/* Header */
.header {
  display: flex;
  padding: 10px 100px 0px 100px;
  background-color: rgb(169, 255, 198);
  color: #1C454F;
}

.title {
  margin-top: 20px;
  margin-bottom: 25px;
  width: 100%;
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
  border: 2px solid black;
  margin-left: 80px;
  line-height: 50px;
}

.title h1 {
  font-size: 70px;
}

.content {
  padding: 20px 100px;
  min-height: 500px;
}

/* Footer */
.footer {
  height: 100px;
  padding-left: 20px;
  background: #e3e3e3;
  font-size: 12px;
  display: flex;
  justify-content: flex-start;
}

.footer a {
  color: #000;
  padding-left: 80px;
}

h1 {
  font-size: 20px;
}

h2 {
  font-size: 14px;
}
</style>
