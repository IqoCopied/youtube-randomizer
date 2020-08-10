<template>
  <div>
    <div>
      <h1 class="static_title">
        <span class="red_span">You<span class="default_span">tube</span></span>
      </h1>
      <div class="randomizer">
          <p>Randomizer</p>
      </div>
    </div>
    <Nuxt />
  </div>
</template>

<style scoped>
.static_title {
  font-size: 40px;
  text-align: center;
  margin-top: 15px;
}
span {
  text-transform: uppercase;
}
.default_span {
  color: #000;
}
.red_span {
  color: #FA3443;
}
.randomizer {
  width: 100px;
  margin: 0 auto;
}
.randomizer > p {
  font-size: 20px;
  margin-top: -17px;
  margin-bottom: 7px;
}
</style>
