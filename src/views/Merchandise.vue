<template>
  <v-container>
    <v-row>
      <h1>Welcome to HungryGhost Records Merch Shop</h1>
    </v-row>
    <v-row>
      <v-col
        v-for="merchandise in merch"
        :key="merchandise.id"
        cols="12"
        md="6"
        lg="4"
      >
        <v-card>
          <v-img
            class="white--text align-end"
            height="200px"
            src=""
          >
          </v-img>
          <v-card-title class="headline">{{ merchandise.merchname }}</v-card-title>
          <v-card-subtitle>
            <v-container>
              <p class="title">{{ merchandise.merchtype }}</p>
              <v-row>
                <p class="title">${{ merchandise.price }}</p>
                <v-spacer />
                <div>
                  <p v-if="merchandise.quantity > 0" class="success--text">
                    {{ merchandise.quantity }} left
                  </p>
                  <p v-else class="error--text">Out of Stock</p>
                </div>
              </v-row>
            </v-container>
          </v-card-subtitle>
          <v-card-text class="mt-n6">{{ merchandise.description }}</v-card-text>
          <v-spacer></v-spacer>
          <v-spacer></v-spacer>

            
          <v-card-actions>
            <v-btn
              v-if="user"
              block
              color="primary"
              text
              :disabled="merchandise.quantity == 0"
              @click="addToCart(merchandise)"
              >Add to Cart</v-btn
            >
            <v-btn
              v-else
              block
              color="primary"
              text
              :disabled="merchandise.quantity == 0"
              to="/login"
              >Please Login to Buy</v-btn
            >
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import { mapGetters } from "vuex";
import { db } from "../plugins/firebase";

export default {
  name: "Merchandise",
  data() {
    return {
      merch: [],
      show: false
    };
  },
  computed: {
    ...mapGetters({
      user: "getUser"
    })
  },
  mounted() {
    this.bind();
  },
  methods: {
    async bind() {
      await this.$bind(
        "merch",
        db.collection("merchandise").where("showCatalog", "==", true)
      );
    },
    async addToCart(merchandise) {
      await db
        .collection("cart")
        .doc(this.user.uid)
        .update({
          items: this.$firebase.firestore.FieldValue.arrayUnion({
            id: merchandise.id,
            name: merchandise.name,
            quantity: 1,
            price: merchandise.price
          })
        });
    }
  }
};
</script>
