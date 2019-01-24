<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <input type="text" v-model="searchValue" @input="autocomplete()">
    <span>{{results}}</span>
    <div>{{searchValue}}</div>
    <ul>
      <li v-for="city in cities" :key="city.geonameId">{{city.name}}</li>
    </ul>
  
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import City from "@/models/cities";

@Component
export default class HelloWorld extends Vue {
  @Prop() private msg!: string;

  private searchValue: string = "";
  private timeoutId: any = 0;
  private cities: Array<City> = [];
  private results: number|undefined = -1;

  autocomplete() {    
    if(this.searchValue==''){
      this.cities=[]
      this.results=undefined;
      return
    }
    //delete previous timeout - for debounce
    if(this.timeoutId)clearTimeout(this.timeoutId);
    this.timeoutId = setTimeout(() => {
      console.log(this.searchValue);
      fetch(
        "http://api.geonames.org/searchJSON?q=" +
          this.searchValue +
          "&maxRows=10&username=dan212"
      )
        .then(response => response.json())
        .then(json => {
          this.cities = json.geonames;
          this.results = json.totalResultsCount;
        }).finally(()=>{console.log("finelly!")});
    }, 800);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
a {
  color: #42b983;
}
</style>
