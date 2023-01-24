<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Tab 1</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Tab 1</ion-title>
        </ion-toolbar>
      </ion-header>
      <span class="fields">
        <ion-input v-model="date1" class="searchBar" type="date"/>
        <ion-input v-model="date2" class="searchBar" type="date"/>
      </span>
      <ion-item class="refresh" v-if="refresh">
        <ion-spinner name="crescent"></ion-spinner>
      </ion-item>
      <ion-list v-for="day in Object.keys(list)" :key="day">
        <ion-item>{{ day }}</ion-item>
        <ion-item v-for="item in list[day]" :key="item.id">
          <ion-grid>
            <ion-row>
              <ion-col>ID</ion-col>
              <ion-col>Nom</ion-col>
              <ion-col>Taille</ion-col>
              <ion-col>Vitesse</ion-col>
              <ion-col>Approche</ion-col>
              <ion-col>Distance d'approche</ion-col>
              <ion-col>Dangereux</ion-col>
            </ion-row>
            <ion-row>
              <ion-col>{{ item.id }}</ion-col>
              <ion-col>{{ item.name }}</ion-col>
              <ion-col>{{ Math.floor(Number(item.estimated_diameter.meters.estimated_diameter_min) * 100) / 100 }} - {{  Math.floor(Number(item.estimated_diameter.meters.estimated_diameter_max) * 100) / 100 }}m</ion-col>
              <ion-col>{{ Math.floor(item.close_approach_data[0].relative_velocity.kilometers_per_second * 100) / 100 }} km/s</ion-col>
              <ion-col>{{ item.close_approach_data[0].close_approach_date }}</ion-col>
              <ion-col>{{ Math.floor(item.close_approach_data[0].miss_distance.lunar * 100) / 100 }} ul</ion-col>
              <ion-col>
                <span v-if="item.is_potentially_hazardous_asteroid">Oui</span>
                <span v-else>Non</span>
              </ion-col>
            </ion-row>
          </ion-grid>
        </ion-item>
      </ion-list>

      <!-- <ExploreContainer name="Tab 1 page" /> -->
      <a target="_blank" href="https://icons8.com/icon/veshHDgXKvFe/mercury">Mercury</a> icon by <a target="_blank" href="https://icons8.com">Icons8</a>
    </ion-content>
  </ion-page>
</template>

<script lang="js">
import { defineComponent } from 'vue';
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonList, IonItem, IonGrid, IonRow, IonCol, IonInput, IonSpinner } from '@ionic/vue';
import axios from 'axios'

export default defineComponent({
  name: 'Tab1Page',
  components: { IonHeader, IonToolbar, IonTitle, IonContent, IonPage, IonList, IonItem, IonGrid, IonRow, IonCol, IonInput, IonSpinner },
  data () {
    return {
      list: [],
      count: 0,
      date1: new Date().toString(),
      date2: new Date().toString(),
      refresh: true
    }
  },
  watch: {
    async date1 (newVal) {
      if(newVal[0] == 2 && this.date2[0] == 2) {
        this.refresh = true;
        const response = await axios.get(`https://api.nasa.gov/neo/rest/v1/feed?start_date=${newVal}&end_date=${this.date2}&api_key=ybnV3FzOynsu5Tyyaip6jo4gYpxHFYlBFOXJzhKa`);
        this.list = response.data.near_earth_objects;
        console.log(this.list)
        this.count = response.data.element_count;
        this.refresh = false
      }
    },
    async date2 (newVal) {
      if(newVal[0] == 2 && this.date1[0] == 2) {
        this.refresh = true
        const response = await axios.get(`https://api.nasa.gov/neo/rest/v1/feed?start_date=${this.date1}&end_date=${newVal}&api_key=ybnV3FzOynsu5Tyyaip6jo4gYpxHFYlBFOXJzhKa`);
        this.list = response.data.near_earth_objects;
        console.log(this.list)
        this.count = response.data.element_count;
        this.refresh = false
      }
    }
  },
  async mounted () {
    this.refresh = true
    const response = await axios.get(`https://api.nasa.gov/neo/rest/v1/feed?start_date=${new Date().toISOString()}&end_date=${new Date().toISOString()}&api_key=ybnV3FzOynsu5Tyyaip6jo4gYpxHFYlBFOXJzhKa`);
    this.list = response.data.near_earth_objects;
    this.count = response.data.element_count;
    this.refresh = false
  }
});
</script>

<style scoped>
body {
  color: white;
}
.searchBar {
  background-color: white;
  color: black;
  width: 30%;
}
span.fields {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
}
.refresh {
  width: 100vw;
  display: flex;
  justify-content: center;
  text-align: center;
}
</style>
