<template>
  <div v-if="isLoading">Loading...</div>
  <div v-else>
    <div class="container">
      <div
        v-if="currentProduct && !showUnavailablePage"
        :class="{
          'men-clothing': currentProduct.category === 'men\'s clothing',
          'women-clothing': currentProduct.category === 'women\'s clothing',
        }"
      >
        <div class="content-container">
          <div class="image">
            <img
              :src="currentProduct.image"
              :alt="currentProduct.title"
              width="305.81"
              height="407"
            />
          </div>
          <div class="text-product">
            <div class="title">{{ currentProduct.title }}</div>
            <div class="rating">
              <div class="category">{{ currentProduct.category }}</div>
              <div class="rate-container">
                <div class="rate">{{ currentProduct.rating.rate }}/5</div>
                <div class="rate-symbol">
                  <span v-for="symbol in ratingSymbols.filled" :key="symbol">
                    <i class="fa-solid fa-circle"></i>
                  </span>
                  <span v-for="symbol in ratingSymbols.empty" :key="symbol">
                    <i class="fa-regular fa-circle"></i>
                  </span>
                </div>
              </div>
            </div>
            <hr />
            <div class="description">{{ currentProduct.description }}</div>
            <hr />
            <div class="price">${{ currentProduct.price }}</div>
            <div class="button-container">
              <div>
                <button class="buying">Buy now</button>
              </div>
              <div>
                <NextProductButton
                  class="product"
                  @nextProduct="incrementIndex"
                />
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-else-if="showUnavailablePage" class="unavailable-page">
        <img src="../assets/sad-face.png" alt="Sad face" />
        <p>This product is unavailable to show</p>
        <NextProductButton class="product" @nextProduct="incrementIndex" />
      </div>
    </div>
  </div>
</template>

<script setup>
import NextProductButton from "./NextProductButton.vue";
import { ref, computed } from "vue";

const currentIndex = ref(1);
const isLoading = ref(false);

const incrementIndex = () => {
  currentIndex.value = (currentIndex.value % 20) + 1;
  fetchProduct();
};

const products = ref([]);

const fetchProduct = async () => {
  try {
    isLoading.value = true;
    const response = await fetch(
      `https://fakestoreapi.com/products/${currentIndex.value}`
    );
    const data = await response.json();
    if (
      data.category === "men's clothing" ||
      data.category === "women's clothing"
    ) {
      products.value.push(data);
      currentProduct.value = data;
      showUnavailablePage.value = false;
    } else {
      showUnavailablePage.value = true;
    }
  } catch (error) {
    console.error("Error fetching product:", error);
    // Handle error gracefully, e.g., show an error message
  } finally {
    isLoading.value = false;
  }
};

// Create a ref for the current product
const currentProduct = ref(null);

// Create a ref to control the visibility of the unavailable page
const showUnavailablePage = ref(false);

// Computed property for rating symbols
const ratingSymbols = computed(() => {
  const filledSymbols = Math.round(currentProduct.value?.rating?.rate) || 0;
  return {
    filled: new Array(filledSymbols).fill("fa-solid fa-circle"),
    empty: new Array(5 - filledSymbols).fill("fa-regular fa-circle"),
  };
});

// Fetch initial product
fetchProduct();
</script>
