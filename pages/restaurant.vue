<template>
  <main class="container restaurant">
    <div class="restaurantheading">
      <h1>Restaurants</h1>
      <AppSelect @change="selectedResaurant = $event" />
      <!-- <pre>{{ selectedResaurant }}</pre> -->
    </div>
    <AppRestaurantInfo :datasource="filteredRestaurants"/>
  </main>
</template>

<script>
import AppRestaurantInfo from '@/components/AppRestaurantInfo.vue';
import AppSelect from '@/components/AppSelect.vue';
import { mapState } from 'vuex';

export default {
  components: {
    AppRestaurantInfo,
    AppSelect
  },
  data() {
    return {
      selectedResaurant: '',
    }
  },
  computed: {
    ...mapState([
      'fooddata',
    ]),
    filteredRestaurants() {
      if (this.selectedResaurant) {
        return this.fooddata.filter(el => {
          let name = el.name.toLowerCase()
          return name.includes(this.selectedResaurant)
        })
      }
      return this.fooddata
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
