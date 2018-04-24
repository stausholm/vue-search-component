<template>
  <div id="app">
    <Modal/>
    <GoogleMap/>
    <Search :countries="mapData"/>
  </div>
</template>

<script>
import GoogleMap from './components/GoogleMap';
import Search from './components/Search';
import Modal from './components/modal';

export default {
  name: 'App',
  data() {
    return {
      mapData: []
    }
  },
  components: {
    GoogleMap, Search, Modal
  },
  created: function() {
    let request = new XMLHttpRequest();
    let x = this;

    request.open('GET', 'https://restcountries.eu/rest/v2/all', true);
    request.send();

    request.onload = function() {
      if (request.status == 200) {
        let data = JSON.parse(request.responseText);
        return x.mapData = data;// = data.slice(0,10);
      }
    };
  }
}
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  list-style-type: none;
}
body {
  font-family: 'Nunito', sans-serif;
  font-size: 13px;
  color: #343434;
}
#app {
  // margin: 0 auto;
  // max-width: 1000px;
  // padding: 20px;
}
</style>
