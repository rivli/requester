<template>
  <div id="app">
    <Requester />
  </div>
</template>

<script>
import Requester from "./components/Requester.vue";

export default {
  name: "App",
  components: {
    Requester,
  },
};
</script>

<style lang="scss">
html,
body {
  margin: 0;
  padding: 0;
}

* {
  box-sizing: border-box !important;
}

.appwrapper {
  min-height: 100vh;
  min-width: 100vw;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  font-size: 13px;
}

.darktheme {
  background-color: #2c3e50;
  color: #fff;

  .dialog {
    background-color: #2c3e50 !important;
  }

  input,
  textarea,
  select {
    color: #fff;
    background-color: #333;
  }

  .answer {
    background-color: #333;
    border: 1px solid orange;
    color: #f5f5f5;
  }
}

.container {
  width: 70vw;
  margin: 0 auto;
  padding-bottom: 10px;
}
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.title {
  font-size: 25px;
  font-weight: bold;
}

.error_msg {
  color: red;
}
.answer {
  display: block;
  padding: 9.5px;
  margin: 0 0 10px;
  font-size: 13px;
  line-height: 1.42857143;
  color: #333;
  word-break: break-all;
  word-wrap: break-word;
  background-color: #f5f5f5;
  border: 1px solid #ccc;
  border-radius: 4px;
  white-space: pre-wrap;
  overflow-x: auto;
}

input,
select,
textarea {
  border: 1px solid orange;
  background-color: #fff;
  padding: 3px 5px;
  outline: none;
  cursor: pointer;

  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  font-size: 13px;
}

textarea {
  min-width: 500px;
  display: block;
  margin-bottom: 10px;
}

select {
  color: orange;
}

.go_btn {
  display: inline-flex;
  background: orange;
  padding: 3px 15px;
  color: white;
  cursor: pointer;
  border: 1px solid orange;
}

.main_set {
  display: flex;
  align-items: center;
  border: 1px solid orange;

  .server_url {
    color: orange;
    border-right: 0;
    padding: 3px 0 3px 5px;
  }
  select {
    border: 0;
  }
  input {
    border: 0;
    padding-left: 0;
    width: 500px;
    flex: 1;

    &.fullBorder {
      padding-left: 5px;
    }
  }
}

.fields_json {
  flex: 1;
  width: 100%;
  box-sizing: border-box;
}

.field {
  margin-bottom: 10px;
  display: flex;
}
</style>
