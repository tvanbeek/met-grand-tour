<template lang="html">
    <main>
      <!-- <intro-page /> -->
      <map-page />
    </main>
</template>

<script>

import 'bootstrap/dist/css/bootstrap.css';
import 'bootstrap-vue/dist/bootstrap-vue.css';

import Intro from './components/Intro';
import Map from './components/Map';

export default {
  components: {
    introPage: Intro,
    mapPage: Map,
  },
};
</script>

<style lang="less">
@import "./style.less";
</style>
