<template>
  <div>
    <input class="product-name"
           type="text"
           v-model="newProduct"
           @keyup.enter="addProduct"
           placeholder="Добавить продукт">
    <button
        class="product-button"
        @click="addProduct"
        :disabled="newProduct === ''">
      Добавить
    </button>
    <ul>
      <li v-for="(product, index) in productList"
          :key="index"
          :class="{ 'checked': product.checked }">
        <span class="product-name"
              @click="toggleProduct(index)">
          {{ product.name }}
        </span>

        <button
            class="product-button"
            v-if="!product.checked"
            @click="deleteProduct(index)">
          Удалить
        </button>
      </li>
    </ul>
  </div>
</template>

<script>
import { ref, watch } from 'vue';

export default {
  setup() {
    const newProduct = ref('');
    const productList = ref([]);

    const loadProductList = () => {
      const storedProductList = localStorage.getItem('productList');
      if (storedProductList) {
        productList.value = JSON.parse(storedProductList);
      }
    };

    const saveProductList = () => {
      localStorage.setItem('productList', JSON.stringify(productList.value));
    };

    const addProduct = () => {
      if (newProduct.value) {
        productList.value.push({
          name: newProduct.value,
          checked: false
        });
        newProduct.value = '';
        saveProductList();
      }
    };

    const toggleProduct = (index) => {
      const product = productList.value[index];
      product.checked = !product.checked;
      saveProductList();
    };

    const deleteProduct = (index) => {
      const product = productList.value.splice(index, 1)[0];
      product.checked = true;
      productList.value.push(product);
      saveProductList();
    };

    watch(productList, saveProductList, { deep: true });

    loadProductList();

    return {
      newProduct,
      productList,
      addProduct,
      toggleProduct,
      deleteProduct
    };
  }
};
</script>

<style>
.checked {
  text-decoration: line-through;
}

li {
  list-style: none;
}

.product-name {
  margin-right: 10px;
}

.product-button {
  padding: 2px 5px;
}
</style>
