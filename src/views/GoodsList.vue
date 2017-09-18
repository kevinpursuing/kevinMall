<template>
  <div class="">
  <nav-header></nav-header>
  <nav-bread><span slot='breads'>Goods</span></nav-bread>
  <div class="accessory-result-page accessory-page">
    <div class="container">
      <div class="filter-nav">
        <span class="sortby">Sort by:</span>
        <a href="javascript:void(0)" class="default cur">Default</a>
        <a href="javascript:void(0)" class="price" @click='sortGoods'>Price <svg class="icon icon-arrow-short" :class="{'sort-up':!sortFlag}"><use xlink:href="#icon-arrow-short">
          <svg id="icon-arrow-short" viewBox="0 0 25 32" width="100%" height="100%"><title>arrow-short</title> <path d="M24.487 18.922l-1.948-1.948-8.904 8.904v-25.878h-2.783v25.878l-8.904-8.904-1.948 1.948 12.243 12.243z" class="path1"></path></svg>
        </use></svg></a>
        <a href="javascript:void(0)" class="filterby stopPop" @click='showFilterPop'>Filter by</a>
      </div>
      <div class="accessory-result">
        <!-- filter -->
        <div class="filter stopPop" id="filter" :class="{'filterby-show':filterBy}">
          <dl class="filter-price">
            <dt>Price:</dt>
            <dd>
              <a href="javascript:void(0)" :class="{'cur':priceChecked=='-1'}" @click='setPriceFilter(-1)'>all</a>
            </dd>
            <dd v-for='(price,index) in priceFilter'>
              <a href="javascript:void(0)" :class="{'cur':priceChecked==index}" @click='setPriceFilter(index)'>{{price.startPrice}} - {{price.endPrice}}</a>
            </dd>
          </dl>
        </div>

        <!-- search result accessories list -->
        <div class="accessory-list-wrap">
          <div class="accessory-list col-4">
            <ul>
              <li v-for="(item,index) in goodsList">
                <div class="pic">
                  <a href="#"><img v-lazy="'/static/'+item.productImage" alt=""></a>
                </div>
                <div class="main">
                  <div class="name">{{item.productName}}</div>
                  <div class="price">{{item.salePrice}}</div>
                  <div class="btn-area">
                    <a href="javascript:;" class="btn btn--m" @click="addCart(item.productId)">加入购物车</a>
                  </div>
                </div>
              </li>
            </ul>
            <div class="loadMore" v-infinite-scroll="loadMore" infinite-scroll-disabled="busy" infinite-scroll-distance="20">
                 <img src="./../assets/loading-spinning-bubbles.svg" alt="" v-show='loading'>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <div class="md-overlay filterOverlay" v-show='overLayFlag' @click='closePop'></div>
  <modal v-bind:mdShow="mdShow" v-on:close="closeModel">
    <p slot="message">请先登录，否则无法加入到购物车中</p>
    <div slot="btnGroup">
      <a class="btn btn--m" href="javascript:void(0)" @click='closeModel'>关闭</a>
    </div>
  </modal>
  <modal v-bind:mdShow="mdShowCart" v-on:close="closeModel">
    <p slot="message">
      <svg class="icon-status-ok">
        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#icon-status-ok"></use>
      </svg>
      <span>加入购物车成功</span>
    </p>
    <div slot="btnGroup">
      <a class="btn btn--m" href="javascript:void(0)" @click='mdShowCart=false'>继续购物</a>
      <router-link class="btn btn--m btn--red" href="javascript:void(0)" to="/cart">查看购物车</router-link>
    </div>
  </modal>
  <nav-footer></nav-footer>
  </div>
</template>
<script>
import '../assets/css/product.css'
import NavHeader from '../components/Header.vue'
import NavFooter from '../components/Footer.vue'
import NavBread from '../components/NavBread.vue'
import Modal from '../components/Modal.vue'
import axios from 'axios'
  export default{
    data(){
      return{
        goodsList:[],
        sortFlag:true,
        page:1,
        pageSize:8,
        busy:true,
        loading:true,
        mdShow:false,
        mdShowCart:false,
        priceFilter:[
          {
            startPrice:'0.00',
            endPrice:'100.00'
          },
          {
            startPrice:'100.00',
            endPrice:'500.00'
          },
          {
            startPrice:'500.00',
            endPrice:'1000.00'
          },
          {
            startPrice:'1000.00',
            endPrice:'5000.00'
          }
        ],
        priceChecked:'-1',
        filterBy:false,
        overLayFlag:false
        }
    },
    mounted:function(){
       this.getGoodList();
    },
    methods:{
       getGoodList(flag){
         this.loading = true;
         var param = {
           page:this.page,
           pageSize:this.pageSize,
           sort:this.sortFlag?1:-1,
           priceLevel:this.priceChecked
         }
         axios.get('goods/list',{
           params:param
         }).then((result)=>{
            var res = result.data.result.list;
            if(result.data.status==0){
              if(flag){
                this.goodsList=this.goodsList.concat(res);
              }else{
                this.goodsList=res;
                this.busy=false;
              }
              if(result.data.result.count<8){
                  this.busy=true;
                  this.loading = false;
              }else{
                  this.busy=false;
              }
            }else{
              this.goodsList=[];
            }
         });
       },
       sortGoods(){
         this.sortFlag = !this.sortFlag;
         this.page = 1;
         this.getGoodList();
       },
       showFilterPop(){
         this.filterBy=true;
         this.overLayFlag=true;
       },
       closePop(){
         this.filterBy=false;
         this.overLayFlag=false;
       },
       setPriceFilter(index){
         this.priceChecked = index;
         this.page = 1;
         this.getGoodList();
         this.closePop();
       },
       loadMore(){
           this.busy = true;
           setTimeout(() => {
             this.page++;
             this.getGoodList(true);
         }, 500);
       },
       addCart(productId){
          axios.post("goods/addCart",{
            productId:productId
          }).then((res)=>{
            console.log(res);
            res=res.data;
            if(res.status==0){
              this.mdShowCart=true;
              this.$store.commit("updateCartCount",1)
            }else{
              this.mdShow=true;
              // alert("msg:"+res.msg);
            }
          })
       },
       closeModel(){
           this.mdShow=false;
           this.mdShowCart=false;
       }
    },
    components:{
      NavHeader,
      NavFooter,
      NavBread,
      Modal
    },
  }
</script>
<style>
 .filterOverlay{
   transition: all 0.5s linear;
 }

 .loadMore{
   height: 100px;
   line-height: 100px;
   text-align: center;
 }

 .sort-up{
   transform: rotate(180deg);
   transition: all .3s ease-out;
 }

 .icon-arrow-short{
   transition: all .3s ease-out;
 }
</style>
