<template>
  <main class="container">
    <section
        class="image"
        :style="`background: url(/${currentItem.img}) no-repeat center center`">
    </section>

    <section class="details">
      <h1>{{ currentItem.item }}</h1>
      <h3>Price: ${{currentItem.price.toFixed(2)}}</h3>

      <div class="quantity">
        <input type="number" min="1" v-model="count" />
        <button
          class="primary"
          :disabled="errors === true"
          @click="addToCard">Add to cart - ${{combinedPrice}}</button>
      </div>

      <fieldset v-if="currentItem.options">
        <legend>
          <h3>Options</h3>
        </legend>
        <div v-for="option in currentItem.options" :key="option">
          <input
            type="radio"
            name="option"
            :id="option"
            :value="option"
            v-model="$v.itemOptions.$model" />
          <label :for="option">{{ option }}</label>
        </div>
      </fieldset>

      <div class="error" v-if="currentItem.options && !$v.itemOptions.required">Options is required</div>

      <fieldset v-if="currentItem.addOns">
        <legend>
          <h3>Add Ons</h3>
        </legend>
        <div v-for="addon in currentItem.addOns" :key="addon">
          <input
            type="checkbox"
            name="addon"
            :id="addon"
            :value="addon" v-model="$v.itemAddons.$model" />
          <label :for="addon">{{ addon }}</label>
        </div>
      </fieldset>

      <div class="error" v-if="currentItem.addOns && !$v.itemAddons.required">Addons is required</div>
      <div class="error" v-if="currentItem.addOns && !$v.itemAddons.minLength">Two addons at least</div>

      <app-toast v-if="cartSubmitted">
        Order submitted
        <br />Check out more
        <nuxt-link to="/restaurant">restaurants</nuxt-link>
      </app-toast>
    </section>

    <restaurant-detail></restaurant-detail>
  </main>
</template>

<script>
import { mapState } from "vuex";
import AppToast from "@/components/AppToast.vue";
import RestaurantDetail from '@/components/RestaurantDetail.vue';
import { required, minLength } from 'vuelidate/lib/validators';

export default {
  components: {
    AppToast,
    RestaurantDetail,
  },
  data() {
    return {
      id: this.$route.params.id,
      count: 1,
      itemOptions: "",
      itemAddons: [],
      itemSizeAndCost: [],
      cartSubmitted: false,
      errors: false,
    };
  },
  validations: {
    itemOptions: {
      required,
    },
    itemAddons: {
      required,
      minLength: minLength(2),
    }
  },
  computed: {
    ...mapState(["fooddata"]),
    currentItem() {
      let result;
      for (let i = 0; i < this.fooddata.length; i++) {
        for (let j = 0; j < this.fooddata[i].menu.length; j++) {
          if (this.fooddata[i].menu[j].id === this.id) {
            result = this.fooddata[i].menu[j];
            break;
          }
        }
      }
      return result;
    },
    combinedPrice() {
      let total = this.count * this.currentItem.price;
      return total.toFixed(2);
    }
  },
  methods: {
    addToCard() {
      let formOutput = {
        item: this.currentItem.item,
        count: this.count,
        options: this.itemOptions,
        addOns: this.itemAddons,
        combinedPrice: this.combinedPrice
      };
      let addOnError = this.$v.itemAddons.$invalid
      let optionError = this.currentItem.options ? this.$v.itemOptions.$invalid : false

      if (addOnError || optionError) {
        this.errors = true
        if (addOnError) {
          this.$v.itemAddons.$touch()
        } else {
          this.$v.itemOptions.$touch()
        }
        this.$v.itemAddons.$touch()
      } else {
        this.errors = false
        this.cartSubmitted = true
        this.$store.commit("addToCart", formOutput)
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.container {
  width: 1000px;
  margin: 100px auto;
  display: grid;
  grid-template-columns: 400px 1fr;
  grid-template-rows: 400px 1fr;
  grid-column-gap: 60px;
  grid-row-gap: 60px;
  line-height: 2;
}
.image {
  grid-area: 1 / 1 / 2 / 2;
  background-size: cover;
}
.details {
  grid-area: 1 / 2 / 2 / 3;
  position: relative;

  .error {
    color: red
  }
}
.options {
  grid-area: 2 / 1 / 3 / 2;
}
</style>
