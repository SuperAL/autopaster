<template>
  <div id="modal" :class="{'fade-in': isShown}">
    <div class="modal-bg fixed full-page"></div>
    <div class="modal-content">
      <i class="icon icon-close fixed right top" @click="$emit('close')">X</i>
    </div>
    
  </div>
</template>

<script>
  export default {
    name: 'input-modal',
    data(){
      return {

      }
    },
    props: {
      isShown: {
        type: Boolean,
        default: false
      }
    },
    // components: { SystemInformation },
    methods: {
      
    }
  }
</script>

<style>
#modal {
  display: none;
}
#modal.fade-in {
  display: block;
}
.modal-bg {
  background-color: rgb(255,255,255);
  opacity: 0;
  z-index: 10;
}
.fade-in .modal-bg {
  opacity: .8;
}
.full-page {
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
}

.modal-content {
  position: relative;
  z-index: 11;
}
.icon {
  font-style: normal;
  display: inline-block;
}
.icon-close {
  cursor: pointer;
  font-size: 30px;
}
.top {
  top: 50px;
}
</style>
