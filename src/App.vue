<template>
  <div id="app">
    <Pomodoro></Pomodoro>
  </div>
</template>

<script>
import Pomodoro from './components/Pomodoro'

export default {
  name: 'app',
  components: {
    Pomodoro
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Open+Sans:300,400');

html, body, p, ol, ul, li, dl, dt, dd, blockquote, figure, fieldset, legend, textarea, pre, iframe, hr, h1, h2, h3, h4, h5, h6 {
  margin: 0;
  padding: 0;
}

h1, h2, h3, h4, h5, h6 {
  font-size: 100%;
  font-weight: normal;
}

ul {
  list-style: none;
}

button, input, select, textarea {
  margin: 0;
}

html, body {
  width: 100%;
  height: 100%;
  font-size: 14px;
}

html {
  box-sizing: border-box;
}

*, *:before, *:after {
  box-sizing: inherit;
}

img, embed, iframe, object, audio, video {
  height: auto;
  max-width: 100%;
}

iframe {
  border: 0;
}

table {
  border-collapse: collapse;
  border-spacing: 0;
}

td, th {
  padding: 0;
  text-align: left;
}


#app {
  width: 100%;
  height: 100%;
  font-family: 'Open Sans', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #ffffff;
  background-color: #051b2c;
}
.input-number {
  width: 100%;
}

.input-number .el-input-number__decrease, .input-number .el-input-number__increase {
  display: none;
}
.input-number .el-input {
  font-size: 2.2rem;
}
.input-number .el-input__inner {
  border: 0;
}
</style>
