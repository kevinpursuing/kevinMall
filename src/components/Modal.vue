<template>
  <div>
    <symbol id="icon-status-ok" viewBox="0 0 32 32">
      <title>status-ok</title>
      <path class="path1" d="M22.361 10.903l-9.71 9.063-2.998-2.998c-0.208-0.209-0.546-0.209-0.754 0s-0.208 0.546 0 0.754l3.363 3.363c0.104 0.104 0.241 0.156 0.377 0.156 0.131 0 0.261-0.048 0.364-0.143l10.087-9.414c0.215-0.201 0.227-0.539 0.026-0.754s-0.539-0.226-0.754-0.026z"></path>
      <path class="path2" d="M16 30.933c-8.234 0-14.933-6.699-14.933-14.933s6.699-14.933 14.933-14.933c8.234 0 14.933 6.699 14.933 14.933s-6.699 14.933-14.933 14.933zM16 0c-8.822 0-16 7.178-16 16 0 8.823 7.178 16 16 16s16-7.177 16-16c0-8.822-7.178-16-16-16z"></path>
    </symbol>
  <div class="md-modal modal-msg md-modal-transition" :class="{'md-show':mdShow}">
    <div class="md-modal-inner">
      <div class="md-top">
        <button class="md-close" @click="closeModel">Close</button>
      </div>
      <div class="md-content">
        <div class="confirm-tips">
          <slot name="message"></slot>
        </div>
        <div class="btn-wrap">
          <slot name="btnGroup"></slot>
        </div>
      </div>
    </div>
  </div>
  <div class="md-overlay" v-show="mdShow" @click="closeModel"></div>
  </div>
</template>

<script>
   export default{
     props:["mdShow"],
     data(){
       return{

       }
     },
     methods:{
       closeModel(){
         this.$emit("close")
       }
     }
   }
</script>

<style>

</style>
