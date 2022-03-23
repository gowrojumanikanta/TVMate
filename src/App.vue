<template>
  <div id="app">
    <nav class="navbar navbar-light navbackground fixed-top justify-content-between">
      <a class="navbar-brand">TV MATE</a>
      <div class="form-inline">
       <button id="backBtn" v-if="this.$router.currentRoute.name !='ShowsDashboard' || isSearch" @click="goHome()" style="margin-right: 10px" class="btn btn-outline-success my-2 my-sm-0" >Back</button>
        <input class="form-control mr-sm-2" v-model="searchText" v-on:keyup.enter="OnmovieScrh()" type="search" placeholder="Search show" aria-label="Search">
        <button @click="OnmovieScrh()" id="searchBtn" class="btn btn-outline-success my-2 my-sm-0" >Search</button>
      </div>
    </nav>

    <div v-if="isSearch">
        <template v-if="showList.length>0">
            <div v-for="val in showList" v-bind:key="val.show.id">
                <div @click="onShowClick(val.show.id)"  class="container-fluid topmargin">
                      <div class="row divShowDetails">
                        <div class="col-md-4">
                            <template v-if="val.show.image"><img width="200" height="200" :src="val.show.image.original"  class="rounded float-left desImg"  alt="Responsive image"></template>
                            <template v-else><div style="background:grey;height:200px;width:200px"></div></template>
                        </div>
                        <div class="col-md-8 showdes text-left">
                          <h1>{{val.show.name}}</h1>

                          <span>{{val.show.premiered}}</span><span class="dot"></span><span>{{val.show.runtime}}mins</span><span class="dot"></span><span v-for="(gen,i) in val.show.genres" :key="i"><span>{{gen}}<span v-if=" i != val.show.genres.length - 1" class="dot"></span></span></span>

                          <p style="margin-top:10px">{{val.show.summary}}</p>
                        </div>
                      </div>
                 </div>
            </div>
        </template>

        <div style="margin-top: 58px;" v-else>
            <h1>No records found</h1>
        </div>
    </div>
   <router-view v-if="!isSearch"></router-view>



  </div>
</template>

<script>

import axios from 'axios';
export default {
  name: 'App',
  components: {

  },
   data() {
    return {
        routePath : "",
        isSearch : "",
        searchText :"",
        showList :[]
     }
    },
   created(){
   },
   methods: {
     OnmovieScrh(){
       if(this.searchText){
        this.isSearch = true
         axios.get(`https://api.tvmaze.com/search/shows?q=${this.searchText}`)
        .then(response => {
                if(response.data.length > 0){
                  this.showList = response.data
                }
                else{
                    this.showList = []
                }
        })
        .catch(e => {
          this.errors.push(e)
        })
       }
     },
     onShowClick(id){
        this.searchText = ""
        this.isSearch = false
        this.$router.push({name: 'ShowDetails', params: { id:id }})
    },
    goHome(){
        if(this.searchText){
            this.isSearch = false
            this.searchText = ""
        }
        else{
         this.$router.push({name: 'ShowsDashboard'})
        }
    }
   }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.navbar-brand{
    color:#ffffff !important;
}
.showdes{
    color: #ffffff;
}
.divShowDetails{
    cursor:pointer;
}

</style>
