<template>
    <div>
        <button type="button" name="button" @click='increament'>+</button>
        <button type="button" name="button" @click='decreament'>-</button>
       <p>{{num}}</p>
    </div>
</template>
<script>
    export default{
      props:['num'],
      data(){
        return {
          msg:'hello vue',
        }
      },
      methods:{
        increament(){
           this.$emit('incre');
        },
        decreament(){
           this.$emit('decre')
        }
      }
    }
</script>
