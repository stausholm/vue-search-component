<template>
  <div id="search">
    <div class="search-bar">
      <p v-if="!isLoading">Found {{filteredListData.length}} matching results</p>
      <p v-else>Loading content.. (fake 500ms delay)</p>
      <div class="input-wrapper">
        <input type="text" v-model="searchTerm" placeholder="Search...">
        <div class="dropdown">
          <button class="dropdown-button" @click="showDropdown = !showDropdown">Sort by: &#x25BC;</button>
          <transition name="slide">
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
          </transition>
        </div>
      </div>
    </div>
    
    <div class="search-results">
      <transition-group v-if="!isLoading" name="slide" tag="ul" class="list-group" appear>
        <SearchItem v-for="(country, i) in sortedListData" :key="i" :country="country" :sortKey="sortKey" :data-index="i" class="list-group-item"/>
      </transition-group>
      <div v-else class="list-group">
        <!-- loading results! -->
        <div class="loader"></div>
      </div>
      <p v-if="filteredListData.length == 0 && !isLoading" class="list-group">Nothing to see here!</p>
    </div>
  </div>
</template>

<script>
import SearchItem from "./SearchItem";

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
  props: ["countries", "isLoading"],
  computed: {
    filteredListData: function() {
      return this.countries.filter(function(item){
        return (
          (item.name.toLowerCase().match(this.searchTerm.toLowerCase())) || (item.nativeName.toLowerCase().match(this.searchTerm.toLowerCase()))
        );
      }.bind(this));
    },
    sortedListData: function() {
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
    
  }
};
</script>

<style lang="scss" scoped>
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
  //border: 2px solid transparent;
  transition: box-shadow .15s ease-out;

  &:focus-within {
    //border: 2px solid #3ecf8e;
    box-shadow: 0px 2px 4px 0px rgba(0,0,0,0.1);
  }

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
      padding: 13px 20px;
      font-size: 13px;
      outline: none;
      border: 2px solid #F1F2F3;
      cursor: pointer;

      &:hover {
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
      //box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
      box-shadow: 0px 2px 4px 0px rgba(0,0,0,0.1);
      right: 0;
      top: calc(100% + 3px);
      padding: 10px 0;
      border: 1px solid #ddd;

      > div {
        display: flex;
        flex-direction: column;

        div {
          display: flex;
          align-items: center;

          &:hover * {
            opacity: .6;
          }
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
        //transition: 0.2s border-width ease-out;
        box-shadow: inset 0 0 0 2px #f1f1f1, inset 0 0 0 0px #ccc;
        transition: 0.4s box-shadow ease-out;
        outline: none;
        margin-right: 5px;
        cursor: pointer;
        margin-left: 12px;

        &:checked {
          //border: 6px solid #ccc;
          box-shadow: inset 0 0 0 2px #f1f1f1, inset 0 0 0 10px #ccc;
        }
      }
      label {
        cursor: pointer;
        padding: 4px 0;
        flex: 1;
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




@keyframes slide-up {
    from {
        transform: translateY(10px);
    }
    to {
        transform: translateY(0);
    }
}
.slide-enter {
    opacity: 0;
}
.slide-enter-active {
    animation: slide-up .25s ease-out forwards;
    transition: opacity .25s;
}
.slide-leave-active {
    animation: slide-up .25s ease-out forwards reverse;
    transition: opacity .25s;
    opacity: 0;
}



.list-group-item {
  animation: slide-up .5s ease-out forwards;
}



.loader {
  border: 16px solid #dedede; /* Light grey */
  border-top: 16px solid #3ecf8e; /* Blue */
  border-radius: 50%;
  width: 120px;
  height: 120px;
  animation: spin 1s linear infinite;
  margin: 80px auto;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>
