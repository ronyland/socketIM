<template>
  <div class="addtag">
    <div class="addtag-header">
      <div class="header-left">
        <img v-on:click="goBack()" class="header-icon" src='./../../assets/img/right.svg'/>
        <span>编辑标签</span>
      </div>
      <span class="addtag-save" v-on:click="saveEdit()">保存</span>
    </div>
     <div class="list-main">
      <div class="list-item" v-for="item in showList" v-bind:key="item.userid" v-on:click="handleChosen(item)">
        <img class="check-icon" v-show="item.chosen" src="./../../assets/img/addtag_check_active.svg"/>
        <img class="check-icon" v-show="!item.chosen" src="./../../assets/img/addtag_check.svg"/>
        <img class="list-photo" v-bind:src="item.img!=null?item.img:'./../../assets/img/my.jpg'">
        <div class="list-contain">
          <span>{{item.notenickname!=null?item.notenickname:item.nickname}}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {mapState} from 'vuex';
import API from './../../utils/API';
export default {
  computed:{
    ...mapState({
      friendList:state=>state.friendStoreModule.friendList,
      userInfo:state=>state.loginStoreModule.userInfo,
      tag:state=>state.pageJumpStoreModule.tag
    })
  },
  data(){
    return{
      tagFriendList:[],
      showList:[]
    }
  },
  mounted(){
    let that=this;
    that.getTagFriendList();
    that.showList=Object.merge([],that.friendList);
  },
  methods:{
    getTagFriendList(){
      let that=this;
      that.$axios.post(API.GET_TAG_FRIENDLIST, {userid:that.userInfo.userid,tagid:that.tag.tagid}, false,false).then(res => {
         if (res.data.code ==200) {
           that.tagFriendList=res.data.data;
           let list=Object.merge([],that.friendList);
           list.forEach(item=>{
             let index=that.tagFriendList.findIndex(_item=>{
               return item.friendid==_item.friendid;
             });
             if(index!=-1){
               item["chosen"]=true;
             }else{
               item["chosen"]=false;
             }
           });
           that.showList=list;
         }
      });
    },
    handleChosen(e){
      e.chosen=!e.chosen;
    },
    saveEdit(){
      let that=this;
      let friendList=new Array();
      that.showList.forEach(item=>{
        if(item.chosen){
          friendList.push(item.friendid);
        }
      });
      that.$axios.post(API.UPDATE_TAGITEM, {tagid:that.tag.tagid,friendList:friendList}, false,false).then(res => {
        if (res.data.code ==200) {
          that.$router.go(-1);
        }
      });
    },
    goBack(){
      let that=this;
      that.$router.go(-1);
    }
  }
}
</script>

<style lang="scss" scoped>
.addtag{
  height: 100%; 
  padding-bottom: 80px;
  box-sizing: border-box;
  // background: $backgroundColor02;
  .addtag-header{
    height: 80px;
    line-height: 80px;
    background: $backgroundColor01;
    display: flex;
    justify-content: space-between;
    padding: 0px 20px;
    font-weight: 700;
    font-size: 30px;
    .header-left{
      display: flex;
      height: 100%;
      .header-icon{
        height: 40px;
        width: 40px;
        margin: auto 10px;
      }
    }
    .addtag-save{
      height: 50px;
      line-height: 50px;
      margin: auto 0px;
      background: $backgroundColor04;
      color: #fff;
      padding: 4px 8px;
      border-radius: 4px;
      box-sizing: border-box;
      font-size: 28px;
      font-weight:normal;
    }
  }
  .list-main{
    overflow-y: auto;
    height: 100%;
  }
  .list-item{
    height: 120px;
    padding-left:20px;
    display: flex;
    box-sizing:border-box;
    .check-icon{
      height:40px;
      width: 40px;
      margin: auto 0px;
      margin-right: 20px;
    }
    .list-photo{
      width: 80px;
      height: 80px;
      margin: auto 0;
      margin-right:20px;
    }
    .list-contain{
      width: 100%;
      border-bottom: 1px solid $borderColor02;
      display: flex;
      span{
        margin: auto 0;
        font-weight: 700;
        font-size: 28px;
      }
    }
  }
}
</style>