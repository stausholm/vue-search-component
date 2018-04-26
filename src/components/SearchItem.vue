<template>
  <li @click="emitData">
    <div class="country-logo">{{country.alpha2Code}}</div>
    <div class="country-title">
      <p>{{country.name}}</p>
      <p>({{country.nativeName}})</p>
    </div>
    <div class="country-info">
      <p :class="{sortParam: sortKey == 'population'}">{{country.population}} People</p>
      <p :class="{sortParam: sortKey == 'area'}">{{country.area || 'area not available'}} km<sup>2</sup></p>
    </div>
  </li>
</template>

<script>
import { bus } from "../main";

export default {
  props: ["country", "sortKey"],
  methods: {
    emitData: function() {
      bus.$emit("itemClicked", this.country);
    }
  }
};
</script>

<style lang="scss" scoped>
li {
  margin-bottom: 4px;
  transition: opacity 0.4s;
  background-color: #fff;
  border-radius: 4px;
  border-left: 15px solid #bdbdbe;
  display: flex;
  justify-content: flex-start;
  padding-right: 20px;
  align-items: center;
  transition: padding-left .125s ease-in-out, padding-right .125s ease-in-out, border-left-color .125s ease-in-out;

  &:hover {
    cursor: pointer;
    padding-left: 12px;
    padding-right: 34px;

    &:nth-child(3n+1) {
      border-left-color: #3ecf8e;

      p.sortParam {
        background: #3ecf8e;
      }
    }
    &:nth-child(3n+2) {
      border-left-color: #7289da;
      
      p.sortParam {
        background: #7289da;
      }
    }
    &:nth-child(3n+3) {
      border-left-color: #e84a5f;
      
      p.sortParam {
        background: #e84a5f;
      }
    }
  }

  .country-logo {
    margin: 18px;
    width: 64px;
    height: 64px;
    background-color: #f1f2f3;
    border-radius: 50%;
    text-align: center;
    line-height: 64px;
    font-size: 1.5em;
    align-items: center;
  }

  .country-title {

    p:first-of-type {
      font-weight: bold;
      font-size: 1.3em;
      margin-bottom: 4px;
    }
    p:last-of-type {
      opacity: .5;
    }
  }

  .country-info {
    font-weight: bold;
    margin-left: auto;
    text-align: right;

    p:first-of-type {
      margin-bottom: 4px;
    }

    p.sortParam {
      background: #bdbdbe;
      color: #fff;
    }
  }
}
</style>
