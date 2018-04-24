<template>
  <div id="search">
    <div class="search-bar">
      <p>Found {{filteredListData.length}} matching results</p>
      <div class="input-wrapper">
        <input type="text" v-model="searchTerm" placeholder="Search...">
        <div class="dropdown">
          <button class="dropdown-button" @click="showDropdown = !showDropdown">Sort by: \/</button>
          <fieldset class="dropdown-content" v-if="showDropdown">
            <div>
              <div>
                <input type="radio" name="sortData" id="population" value="population" v-model="sortKey">
                <label for="population">Population</label>
              </div>
              <div>
                <input type="radio" name="sortData" id="name" value="name" v-model="sortKey">
                <label for="name">Name</label>
              </div>
              <div>
                <input type="radio" name="sortData" id="area" value="area" v-model="sortKey">
                <label for="area">Area</label>
              </div>
            </div>
            <hr>
            <div>
              <div>
                <input type="radio" name="sortDirection" id="asc" value="asc" v-model="sortDir">
                <label for="asc">Asc</label>
              </div>
              <div>
                <input type="radio" name="sortDirection" id="desc" value="desc" v-model="sortDir">
                <label for="desc">Desc</label>
              </div>
            </div>
          </fieldset>
        </div>
      </div>
    </div>
    
    <div class="search-results">
      <transition-group name="slide" tag="ul" class="list-group" v-bind:css="false" v-on:before-enter="beforeEnter" v-on:enter="enter" v-on:leave="leave">
        <SearchItem v-for="(country, i) in sortedListData" :key="i" :country="country" :data-index="i" class="list-group-item"/>
      </transition-group>
      <p v-if="filteredListData.length == 0" class="list-group">Nothing to see here!</p>
    </div>
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
      sortDir: 'asc',
      showDropdown: false
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
        delay = 50 * 5;
      } else {
        delay = el.dataset.index * 50
      }
      setTimeout(function () {
        el.style.opacity = 1;
        done();
      }, delay)
    },
    leave: function (el, done) {
      var delay;
      if (el.dataset.index > 5) {
        delay = 50 * 5;
      } else {
        delay = el.dataset.index * 50
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
.search-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  max-width: 1000px;
  margin: 0 auto;
  padding: 20px;
  margin-top: 200px;
}
.input-wrapper {
  display: flex;
  input[type="text"] {
    background: #F1F2F3;
    border: 2px solid #F1F2F3;
    padding: 10px 20px;
    border-top-left-radius: 4px;
    border-bottom-left-radius: 4px;
    outline: none;
    font-size: 13px;

    &:hover {
      border-color: #F1F2F3;
    }
  }

  .dropdown {
    position: relative;
    display: inline-block;

    .dropdown-button {
      background-color: #ddd;
      color:#fff;
      padding: 10px 20px;
      font-size: 13px;
      outline: none;
      border: 2px solid #F1F2F3;
      cursor: pointer;

      &:hover, &:focus {
        background-color: #ccc;
      }
    }

    .dropdown-content {
      display: block;
      border: none;
      position: absolute;
      background-color: #f1f1f1;
      min-width: 160px;
      z-index: 1;
      box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
      right: 0;
      top: 100%;

      > div {
        display: flex;
        flex-direction: column;

        // &:nth-child(1) {
        //   border-bottom: 1px solid grey;
        // }

        div {
          display: flex;
          align-items: center;
          padding: 4px 12px;
        }
      }

      hr {
        border: none;
        border-bottom: 1px solid #ccc;
        margin: 7px 12px;
      }

      input[type="radio"] {
        appearance: none;
        border-radius: 50%;
        width: 16px;
        height: 16px;
        border: 2px solid #ccc;
        transition: 0.2s border-width ease-out;
        outline: none;
        margin-right: 5px;
        cursor: pointer;

        &:checked {
          border: 6px solid #ccc;
        }
      }
      label {
        cursor: pointer;
      }
    }
  }
}
.search-results {
  background-color: #F1F2F3;
  min-height: 80vh;

  .list-group {
    margin: 0 auto;
    max-width: 1000px;
    padding: 20px;
  }
}
</style>
