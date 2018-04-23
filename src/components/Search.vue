<template>
  <div id="search">
    <div>
      <input type="text" v-model="searchTerm">
      <div>
        <label>
          <input type="radio" name="sortDirection" value="asc" v-model="sortDir">
          <span>asc</span>
        </label>
        <label>
          <input type="radio" name="sortDirection" value="desc" v-model="sortDir">
          <span>desc</span>
        </label>
      </div>
      <div>
        <label>
          <input type="radio" name="sortData" value="population" v-model="sortKey">
          <span>population</span>
        </label>
        <label>
          <input type="radio" name="sortData" value="name" v-model="sortKey">
          <span>name</span>
        </label>
        <label>
          <input type="radio" name="sortData" value="area" v-model="sortKey">
          <span>area</span>
        </label>
      </div>
    </div>
    <p>Matching results: {{filteredListData.length}}</p>
    <!-- <ul v-if="filteredListData.length > 0">
        <SearchItem v-for="(country, i) in sortedListData" :key="i" :country="country"/>
    </ul>
    <p v-else>Nothing to see here!</p> -->
    <transition-group name="slide" tag="ul" class="list-group" v-bind:css="false" v-on:before-enter="beforeEnter" v-on:enter="enter" v-on:leave="leave">
      <SearchItem v-for="(country, i) in sortedListData" :key="i" :country="country" :data-index="i" class="list-group-item"/>
    </transition-group>
  </div>
</template>

<script>
import SearchItem from "./SearchItem";
//import _ from 'lodash';

export default {
  data () {
    return {
      searchTerm: '',
      sortKey: 'name',
      sortDir: 'asc'
    }
  },
  components: {
    SearchItem
  },
  props: ["countries"],
  computed: {
    filteredListData: function() {
      return this.countries.filter(function(item){
        return (
          (item.name.toLowerCase().match(this.searchTerm.toLowerCase())) || (item.nativeName.toLowerCase().match(this.searchTerm.toLowerCase()))
        );
      }.bind(this));
    },
    sortedListData: function() {
      // return _.orderBy(this.filteredListData, function(item) {
      //   return item[this.filterKey].toLowerCase();
      // }.bind(this), this.filterDir);

      console.log('inside sortedListData()');
      
      let key = this.sortKey;
      let direction = this.sortDir;
      if (key === 'name') {
        return this.filteredListData.sort(function(a,b){
          var nameA = a.name.toUpperCase(); // ignore upper and lowercase
          var nameB = b.name.toUpperCase(); // ignore upper and lowercase
          if (direction === 'asc') {
            if (nameA < nameB) {
            return -1;
            }
            if (nameA > nameB) {
              return 1;
            }
            // names must be equal
            return 0;
          } else {
            if (nameA > nameB) {
            return -1;
            }
            if (nameA < nameB) {
              return 1;
            }
            // names must be equal
            return 0;
          }
          
        })
      }
      if(key === 'population' || key === 'area') {
        return this.filteredListData.sort(function(a,b){
          if(direction === 'asc') {
            return a[key] - b[key];
          } else {
            return b[key] - a[key];
          }
        })
      }
    }
  },
  methods: {
    //https://vuejs.org/v2/guide/transitions.html#Staggering-List-Transitions
    beforeEnter: function (el) {
      el.style.opacity = 0
      //el.style.height = 0
    },
    enter: function (el, done) {
      var delay;
      if (el.dataset.index > 5) {
        delay = 150 * 5;
      } else {
        delay = el.dataset.index * 150
      }
      setTimeout(function () {
        el.style.opacity = 1;
        done();
      }, delay)
    },
    leave: function (el, done) {
      var delay;
      if (el.dataset.index > 5) {
        delay = 150 * 5;
      } else {
        delay = el.dataset.index * 150
      }
      setTimeout(function () {
        el.style.opacity = 0;
        done();
      }, delay)
    }
  }
};
</script>

<style lang="scss" scoped>
// @keyframes slide-up {
//   from {
//     transform: translateY(20px);
//   }
//   to {
//     transform: translateY(0);
//   }
// }

// /*mixing transitions and keyframes*/
// .slide-enter {
//   opacity: 0;
// }
// .slide-enter-active {
//   animation: slide-up 1s ease-out forwards;
//   transition: opacity .5s;
// }
// /* .slide-leave {

// } */
// .slide-leave-active {
//   animation: slide-up 1s ease-out forwards reverse;
//   transition: opacity 1s;
//   opacity: 0;
//   position: absolute;
// }
// .slide-move {
//   transition: transform 1s;
// }
</style>
