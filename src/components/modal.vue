<template>
<div class="modal" v-if="showing" @click="closeModal">
  <div class="modal-content">
    <div class="modal-header">
      <h1>{{item.name}} <span>{{item.nativeName}}</span></h1>
    </div>
    <div class="modal-body">
      <p>this is modal body</p>
      <img :src="item.flag" :alt="item.name + ' flag'">
    </div>
    <div class="modal-footer">
      <p>this is modal footer</p>
    </div>
  </div>
</div>  
</template>

<script>
import {bus} from '../main';

export default {
  data () {
    return {
      showing: false,
      item: {}
    }
  },
  created(){
    bus.$on('itemClicked', (data) => {
      this.showing = true;
      this.item = data;
    })
  },
  methods: {
    closeModal: function(e) {
      if (e.target.classList.contains('modal')) {
        this.showing = false;
      }
      
    }
  }
};
</script>

<style lang="scss" scoped>
.modal {
  position: fixed;
  z-index: 1;
  left:0;
  top:0;
  width:100%;
  height:100%;
  overflow:auto;
  background-color:rgba(0,0,0,0.4);
  .modal-content {
    background-color:#fefefe;
    margin: 15% auto;
    padding:20px;
    width: 100%;
    max-width:900px;
    img {
      max-width:100%;
    }
  }
}
</style>
