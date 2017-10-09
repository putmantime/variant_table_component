<template>
  <div id="app">
    <div>
      <exac-table var_id="ClinVarVariant:251469"></exac-table>
    </div>
  </div>
</template>

<script>
  import exactable from './components/ExAcTable';


  export default {
    name: 'app',
    components: {
      'exac-table': exactable,
    },
  };
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>
