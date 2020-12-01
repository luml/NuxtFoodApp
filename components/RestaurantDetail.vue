<template>
  <section class="options">
    <!-- description, delievery time, rating and meal options -->
    <h3>Description</h3>
    <p>{{ currentItem.description }}</p>
    <h3>Delievery Time</h3>
    <p>{{ otherInfos.deliveryTime }} mins</p>
    <h3>Rating</h3>
    <p>{{ otherInfos.rating }}</p>
    <h3>Meal Options</h3>
    <p>{{ otherInfos.meal.join('、') }}</p>
  </section>
</template>

<script>
import { mapState } from 'vuex';

export default {
  data() {
    return {
      id: this.$route.params.id,
      otherInfos: "",
    }
  },
  computed: {
    ...mapState(['fooddata']),
    currentItem() {
      let result
      for (let i = 0; i < this.fooddata.length; i++) {
        for (let j = 0; j < this.fooddata[i].menu.length; j++) {
          if (this.fooddata[i].menu[j].id === this.id) {
            this.otherInfos = this.fooddata[i]
            result = this.fooddata[i].menu[j];
            break;
          }
        }
      }
      return result;
    }
  },
};
</script>

<style lang="scss" scoped>

</style>
