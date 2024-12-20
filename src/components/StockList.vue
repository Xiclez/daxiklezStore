<template>
  <div class="estore">
    <!-- Language and Currency Switches -->
    <div class="switches">
      <div class="switch">
        <label for="currency-switch">Currency: </label>
        <select v-model="currency" id="currency-switch">
          <option value="USD">USD</option>
          <option value="MXN">MXN</option>
        </select>
      </div>
    </div>
    <div class="banner">
        <h2>Low prices with high quality stock!</h2>
        <p>A project by <a href="https://daxiklez.com" target="_blank">DaXiklez</a></p>
      </div>
    <div v-if="loading" class="loading">Loading...</div>
    <div v-else class="stock-items">
      
      <div 
        v-for="item in filteredItems" 
        :key="item._id" 
        class="stock-card" 
        :style="{ backgroundImage: 'url(' + item.imgUrl + ')', backgroundSize: 'cover' }">
        
        <div class="card-content">
          <div class="card-header">
            <h3>{{ item.name }}</h3>
          </div>
          <div class="card-body">
            <p>Price: {{ formatPrice(item.optimalPrice) }}</p>
            <p :style="getConditionStyle(item.condition)">
              Condition: {{ getConditionText(item.condition) }}
            </p>
          </div>
          
          <!-- Buy and Make Offer Buttons -->
          <div class="card-buttons">
            <button
              class="buy-button"
              @click="openWhatsApp(item, 'buy')"
            >
              Buy
            </button>
            <button
              class="offer-button"
              @click="openWhatsApp(item, 'offer')"
            >
              Make Offer
            </button>
          </div>

          
          <!-- View Product Button -->
          <div class="view-product" v-if="item.imgUrl"> 
  <button class="view-button" @click="viewProduct(item.imgUrl)">View Product</button>
</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "StockList",
  data() {
    return {
      items: [],
      loading: true,
      language: 'EN',
      currency: 'USD',
    };
  },
  computed: {
    filteredItems() {
      return this.items.filter(item => !item.actualSale);
    },
  },
  methods: {
    async fetchItems() {
      try {
        const response = await axios.get("https://daxiklez-store-api.vercel.app/stock");
        this.items = response.data;
      } catch (error) {
        console.error("Error fetching stock items:", error);
      } finally {
        this.loading = false;
      }
    },
    openWhatsApp(item, actionType) {
      let message;
      if (actionType === 'buy') {
        message = `Hi, I'm interested in buying ${item.name}. Is it still available?`;
      } else if (actionType === 'offer') {
        message = `Hi, I would like to make an offer for ${item.name}. Can you do [PRICE IN USD/MXN]?`;
      }

      // Replace 'your_phone_number' with your actual WhatsApp number
      const whatsappLink = `https://wa.me/+523330547185?text=${encodeURIComponent(message)}`;
      window.open(whatsappLink, '_blank');
    },
    viewProduct(imgUrl) {
      window.open(imgUrl, "_blank");
    },
    getConditionText(condition) {
      const conditions = {
        0: "Non functional - scrap parts",
        1: "Reparable faults",
        2: "Aesthetic damage",
        3: "Used",
        4: "Like new",
        5: "New",
      };
      return conditions[condition] || "Unknown";
    },
    getConditionStyle(condition) {
      const conditionColor = {
        0: "red",
        1: "orange",
        2: "yellow",
        3: "blue",
        4: "green",
        5: "darkgreen",
      };
      const color = conditionColor[condition] || "black";
      return {
        color: color,
        fontWeight: 'bold',
      };
    },
    formatPrice(price) {
      if (this.currency === 'USD') {
        return `$${price.toFixed(2)}`;
      } else if (this.currency === 'MXN') {
        const mxnPrice = price * 20; // Multiply by 20 to convert USD to MXN
        return `MXN ${mxnPrice.toFixed(2)}`;
      }
      return price.toFixed(2);
    },
  },
  mounted() {
    this.fetchItems();
  },
};
</script>

<style scoped>
.estore {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.switches {
  position: absolute;
  top: 10px;
  right: 10px;
  display: flex;
  gap: 20px;
}

.switch select {
  padding: 5px;
  font-size: 14px;
}


.loading {
  font-size: 24px;
  text-align: center;
  margin-top: 50px;
}

.stock-items {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
}

.stock-card {
  border: 1px solid #ccc;
  padding: 20px;
  width: 250px;
  height: 350px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  background-color: #fff;
  position: relative;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.card-content {
  background: rgba(255, 255, 255, 0.8); 
  padding: 15px;
  border-radius: 8px;
  margin-top: auto;
}

.card-header h3 {
  font-size: 18px;
  margin-bottom: 10px;
}

.card-body p {
  margin: 10px 0;
}

.qr-code {
  margin-top: 15px;
  text-align: center;
}

.card-header, .card-body {
  background: rgba(255, 255, 255, 0.279);  padding: 10px;
  border-radius: 5px;
}

/* Button Styling */
.card-buttons {
  display: flex;
  justify-content: space-between;
  margin-top: 10px;
}

.buy-button, .offer-button {
  padding: 8px 15px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.buy-button {
  background-color: #28a745; /* Green */
  color: white;
}

.offer-button {
  background-color: #ffc107; /* Yellow */
  color: white;
}

.buy-button:hover {
  background-color: #218838;
}

.offer-button:hover {
  background-color: #e0a800;
}

/* View Product Button Styling */
.view-product {
  margin-top: 10px;
}

.view-button {
  padding: 8px 15px;
  border: none;
  border-radius: 5px;
  background-color: #007bff;
  color: white;
  cursor: pointer;
}

.view-button:hover {
  background-color: #0056b3;
}
</style>
