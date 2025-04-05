<template>
  <div class="container mt-4">
    <h1 class="mb-4">Asset Allocation Calculator</h1>
    <nav>
      <div class="nav nav-tabs" id="nav-tab" role="tablist">
        <button
          class="nav-link"
          :class="{ active: currentTab === 'crypto' }"
          @click="currentTab = 'crypto'"
        >
          Crypto
        </button>
        <button
          class="nav-link"
          :class="{ active: currentTab === 'stocks' }"
          @click="currentTab = 'stocks'"
        >
          Stocks
        </button>
      </div>
    </nav>
    <div class="tab-content mt-3" id="nav-tabContent">
      <AssetCategory
        v-if="currentTab === 'crypto'"
        :assets="cryptoAssets"
        v-model:budget="cryptoBudget"
        v-model:currency="cryptoCurrency"
        @add-asset="addAsset('crypto', $event)"
        @delete-asset="deleteAsset('crypto', $event)"
        @reset-percentages="resetPercentages('crypto')"
      />
      <AssetCategory
        v-if="currentTab === 'stocks'"
        :assets="stocksAssets"
        v-model:budget="stocksBudget"
        v-model:currency="stocksCurrency"
        @add-asset="addAsset('stocks', $event)"
        @delete-asset="deleteAsset('stocks', $event)"
        @reset-percentages="resetPercentages('stocks')"
      />
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import AssetCategory from './components/AssetCategory.vue';

export default {
  components: {
    AssetCategory
  },
  setup() {
    const cryptoAssets = ref([]);
    const stocksAssets = ref([]);
    const cryptoBudget = ref(0);
    const stocksBudget = ref(0);
    const cryptoCurrency = ref('EUR');
    const stocksCurrency = ref('USD');
    const currentTab = ref('crypto');

    onMounted(async () => {
      try {
        const response = await fetch(`${process.env.BASE_URL}config.json`);
        const data = await response.json();
        cryptoAssets.value = data.crypto.assets.map(asset => ({ ...asset }));
        cryptoCurrency.value = data.crypto.currency;
        stocksAssets.value = data.stocks.assets.map(asset => ({ ...asset }));
        stocksCurrency.value = data.stocks.currency;
      } catch (error) {
        console.error('Error loading config:', error);
      }
    });

    const addAsset = (category, name) => {
      const newAsset = { name, percentage: 0 };
      if (category === 'crypto') {
        cryptoAssets.value.push(newAsset);
      } else if (category === 'stocks') {
        stocksAssets.value.push(newAsset);
      }
    };

    const deleteAsset = (category, index) => {
      if (category === 'crypto') {
        cryptoAssets.value.splice(index, 1);
      } else if (category === 'stocks') {
        stocksAssets.value.splice(index, 1);
      }
    };

    const resetPercentages = (category) => {
      if (category === 'crypto') {
        cryptoAssets.value.forEach(asset => (asset.percentage = 0));
      } else if (category === 'stocks') {
        stocksAssets.value.forEach(asset => (asset.percentage = 0));
      }
    };

    return {
      cryptoAssets,
      stocksAssets,
      cryptoBudget,
      stocksBudget,
      cryptoCurrency,
      stocksCurrency,
      currentTab,
      addAsset,
      deleteAsset,
      resetPercentages
    };
  }
};
</script>