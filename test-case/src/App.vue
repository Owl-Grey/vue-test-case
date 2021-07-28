<template>
  <v-app>
    <Header />
    <div id="app">
      <v-row justify="center">
        <v-col
        cols="11"
        >

        <v-card ref="form">
          <v-card-text>
            <div class="head">
              <h1>Пользователь {{name +" "+secname}}</h1>

            </div>
            <v-row>
              <v-col
              cols="6"
              >
              <v-text-field
              v-model="name"
              :rules="[() => !!name || 'This field is required']"
              label="Имя"
              placeholder="Иван"
              required
              ></v-text-field>
            </v-col>
            <v-col
            cols="6"
            >
            <v-text-field
            v-model="secname"
            :rules="[() => !!secname || 'This field is required']"
            label="Фамилия"
            placeholder="Иванов"
            required
            ></v-text-field>
          </v-col>
        </v-row>
        <v-autocomplete
        v-model="civil"
        :rules="[() => !!civil || 'This field is required']"
        :items="civil_countries"
        label="Гражданство"
        @change="onChange()"
        required
        ></v-autocomplete>

        <v-autocomplete
        v-model="country"
        :rules="[() => !!country || 'This field is required']"
        :items="countries"
        item-text="name"
        item-value="id"
        label="Страна въезда"
        @change="onChange()"
        required
        ></v-autocomplete>
        <v-autocomplete
        v-model="type"
        :rules="[() => !!type || 'This field is required']"
        :items="types"
        item-text="name"
        item-value="id"
        label="Тип визы"
        @change="onChange()"
        required
        ></v-autocomplete>
        <v-menu
          v-model="menuarr"
          :close-on-content-click="false"
          :nudge-right="40"
          transition="scale-transition"
          offset-y
          min-width="auto"
          >
          <template v-slot:activator="{ on, attrs }">
            <v-text-field
            v-model="arrival"
            label="Дата прилета"
            prepend-icon="mdi-calendar"
            readonly
            v-bind="attrs"
            v-on="on"
            ></v-text-field>
          </template>
          <v-date-picker
          v-model="arrival"
          :max="departure"
          @input="menu2 = false"
          ></v-date-picker>
      </v-menu>
      <v-menu
      v-model="menudep"
      :close-on-content-click="false"
      :nudge-right="40"
      transition="scale-transition"
      offset-y
      min-width="auto"
      >
      <template v-slot:activator="{ on, attrs }">
        <v-text-field
        v-model="departure"
        label="Дата прилета"
        prepend-icon="mdi-calendar"
        readonly
        v-bind="attrs"
        v-on="on"
        ></v-text-field>
      </template>
      <v-date-picker
      v-model="departure"
      :min='arrival'
      @input="menu2 = false"
      ></v-date-picker>
    </v-menu>
    <v-autocomplete
    v-model="visit"
    :rules="[() => !!visit || 'This field is required']"
    :items="trys"
    item-text="name"
    item-value="id"
    label="Количество заездов"
    @change="onChange()"
    required
    ></v-autocomplete>
    <v-autocomplete
    ref="country"
    v-model="time"
    :rules="[() => !!time || 'This field is required']"
    :items="timespent"
    item-text="name"
    item-value="id"
    label="Время обработки"
    @change="onChange()"
    placeholder="Select..."
    required
    ></v-autocomplete>
    <h1>{{summ}}</h1>
  </v-card-text>
  <v-divider class="mt-12"></v-divider>
  <v-card-actions>
    <v-btn
    color="primary"
    text
    @click="submit"
    >
    Submit
  </v-btn>
</v-card-actions>
</v-card>
</v-col>
</v-row>
</div>
</v-app>
</template>

<script>
import axios from 'axios'
// import json from '../public/information.json'
import Header from '@/components/header.vue'
export default {
  name: 'App',
  components: {
    Header
  },
  methods:{
    onChange(){
      let cost=0
      let ctr=this.countries
      let types = this.types
      let trys = this.trys
      let times=this.timespent
      for (var i in ctr) {
        if (ctr[i].id==this.country){
          this.res.country=ctr[i].name
        }
      }
      for (var j in types) {
        if (types[j].id==this.type){
          this.res.type=types[j].name
          cost+=parseFloat(types[j].price)
        }
      }
      for (var k in trys) {
        if (trys[k].id==this.visit && trys[k].relative==this.country){
          this.res.visitcount=trys[k].name
          cost+=parseFloat(trys[k].price)
        }
      }
      for (var l in times) {
        if (times[l].id==this.time && times[l].relative==this.country){
          this.res.time=times[l].name
          cost+=parseFloat(times[l].price)
        }
      }
      this.summ=cost
    },
    submit(){
      this.res.name=this.name
      this.res.civil=this.civil
      this.res.secname=this.secname
      this.res.cost=this.summ
      this.res.arrival = this.arrival
      this.res.departure = this.departure
      console.log(this.res)
    }
  },
  mounted() {
    axios.get('information.json')
      .then((result) => {
        this.countries=result.data.countries
        this.types=result.data.types
        this.trys=result.data.try
        this.timespent=result.data.timespent
      })
  },
  data() {
    return{
      summ:0,
      menuarr:"",
      menudep:"",
      arrival:"",
      departure:"",
      civil:"",
      name:"",
      secname:"",
      country:"",
      time:"",
      visit:"",
      type:"",
      select:{},
      countries:{},
      types:{},
      trys:{},
      timespent:{},
      res:{name:"",secname:"",civil:"",country:"",type:"",arrival:"",departure:"",visitcount:"",time:"",cost:""},
      civil_countries:["Австралия","США","Россия","Франция","Швейцария","Гондурас","Бангладеш","Ботсвана","Испания","ОАЭ","КНДР","Китай"]
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
  margin-top: 60px;
}
</style>
