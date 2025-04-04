<template>
    <div>
      <div class="mb-3">
        <label class="form-label">
          Currency:
          <select v-model="localCurrency" class="form-select d-inline-block w-auto ms-2 me-2">
            <option value="USD">USD</option>
            <option value="EUR">EUR</option>
            <option value="GBP">GBP</option>
            <option value="JPY">JPY</option>
            <option value="CHF">CHF</option>
            <option value="CAD">CAD</option>
            <option value="AUD">AUD</option>
            <option value="CNY">CNY</option>
            <option value="BGN">BGN</option>
          </select>
        </label>
        <label class="form-label">
          Budget:
          <input
            type="number"
            step="0.01"
            v-model="localBudget"
            class="form-control d-inline-block w-auto ms-2 me-2"
          />
          {{ localCurrency }}
        </label>
      </div>
      <button class="btn btn-primary mb-3" @click="addAsset">Add Asset</button>
      <button class="btn btn-secondary mb-3 ms-2" @click="resetPercentages">Reset All Percentages</button>
      <table class="table table-striped">
        <thead>
          <tr>
            <th>Asset</th>
            <th>Percentage (%)</th>
            <th>Amount ({{ localCurrency }})</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(asset, index) in assets" :key="asset.name">
            <td>{{ asset.name }}</td>
            <td>
              <input
                type="number"
                min="0"
                max="100"
                step="0.1"
                v-model="asset.percentage"
                class="form-control"
              />
            </td>
            <td>{{ ((asset.percentage / 100) * localBudget).toFixed(2) }} {{ localCurrency }}</td>
            <td>
              <button class="btn btn-danger btn-sm" @click="deleteAsset(index)">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
      <p :style="{ color: totalPercentage === 100 ? 'black' : 'red' }">
        Total Percentage: {{ totalPercentage.toFixed(1) }}%
      </p>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      assets: Array,
      budget: Number,
      currency: String
    },
    computed: {
      localBudget: {
        get() {
          return this.budget;
        },
        set(value) {
          this.$emit('update:budget', parseFloat(value));
        }
      },
      localCurrency: {
        get() {
          return this.currency;
        },
        set(value) {
          this.$emit('update:currency', value);
        }
      },
      totalPercentage() {
        return this.assets.reduce((sum, asset) => sum + asset.percentage, 0);
      }
    },
    methods: {
      addAsset() {
        const name = prompt('Enter the name of the new asset:');
        if (name) {
          this.$emit('add-asset', name);
        }
      },
      deleteAsset(index) {
        this.$emit('delete-asset', index);
      },
      resetPercentages() {
        this.$emit('reset-percentages');
      }
    }
  };
  </script>