<template>
  <div>
      <header>
          <img src="skidata.png" alt="Skidata" />
      </header>
  </div>
</template>

<script>

export default {
    name: 'Header'
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
header {
    width: 100%;
    height:50px;
    background: #2c3033;
    text-align: left;
}

header img {
    margin: 12px;
}
</style>
