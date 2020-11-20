<template>
  <div id="app">
    <Simon />
  </div>
</template>

<script>
import Simon from './components/Simon';

export default {
  name: "App",
  components: {
    Simon
  },
  methods: {}
};
</script>

<style>
* {
  margin:0;
  padding:0;
  border:none;
  outline:none;
  resize:none;
  box-sizing:border-box;
}

.b5radius { border-radius:5px; }
.b50radius { border-radius:50%; }
.transition { transition:0.1s linear; }

#app {
  width:100%;
  min-height:100vh;
  background:#F4F4F4;
  overflow:hidden;
}
</style>