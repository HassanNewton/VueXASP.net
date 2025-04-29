<template>
  <div class="product-container">
    <h1>🛒 Produktlista</h1>

    <form @submit.prevent="addProduct" class="product-form">
      <input v-model="newProduct.name" placeholder="Produktnamn" required />
      <input
        v-model.number="newProduct.price"
        placeholder="Pris"
        required
        type="number"
      />
      <button type="submit">➕ Lägg till</button>
    </form>

    <ul class="product-list">
      <li v-for="product in products" :key="product.id" class="product-card">
        <div>
          <strong>{{ product.name }}</strong
          ><br />
          {{ product.price }} kr
        </div>
        <div class="card-buttons">
          <button @click="deleteProduct(product.id)">🗑️</button>
          <button @click="startEdit(product)">✏️</button>
        </div>
      </li>
    </ul>

    <div v-if="editProduct.id" class="edit-box">
      <h2>Redigera produkt</h2>
      <input v-model="editProduct.name" placeholder="Nytt namn" />
      <input
        v-model.number="editProduct.price"
        placeholder="Nytt pris"
        type="number"
      />
      <div class="card-buttons">
        <button @click="updateProduct">💾 Spara</button>
        <button @click="cancelEdit">❌ Avbryt</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [],
      newProduct: { name: "", price: 0 },
      editProduct: { id: null, name: "", price: 0 },
    };
  },
  methods: {
    async fetchProducts() {
      const response = await fetch("https://localhost:7172/api/Products");
      this.products = await response.json();
    },
    async addProduct() {
      const response = await fetch("https://localhost:7172/api/Products", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(this.newProduct),
      });
      if (response.ok) {
        this.newProduct = { name: "", price: 0 };
        this.fetchProducts();
      }
    },
    async deleteProduct(id) {
      await fetch(`https://localhost:7172/api/Products/${id}`, {
        method: "DELETE",
      });
      this.fetchProducts();
    },
    startEdit(product) {
      this.editProduct = { ...product };
    },
    async updateProduct() {
      await fetch(
        `https://localhost:7172/api/Products/${this.editProduct.id}`,
        {
          method: "PUT",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({
            name: this.editProduct.name,
            price: this.editProduct.price,
          }),
        }
      );
      this.editProduct = { id: null, name: "", price: 0 };
      this.fetchProducts();
    },
    cancelEdit() {
      this.editProduct = { id: null, name: "", price: 0 };
    },
  },
  mounted() {
    this.fetchProducts();
  },
};
</script>

<style scoped>
/* Färgpalett */
:root {
  --light: #c6ac8f;
  --dark: #0a0908;
  --accent: #5f4626;
}

.product-container {
  background: rgba(141, 111, 76, 0.92);
  padding: 2rem;
  border-radius: 12px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
  max-width: 700px;
  width: 100%;
  margin: auto;
  backdrop-filter: blur(4px); /* ger en snygg glas-effekt */
  color: var(--dark);
}

h1 {
  margin-bottom: 1.5rem;
  font-size: 2rem;
  color: var(--dark);
}

.product-form,
.edit-box {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  margin-bottom: 2rem;
}

input {
  padding: 0.6rem;
  border: 2px solid var(--accent);
  border-radius: 6px;
  font-size: 1rem;
  background: #fff;
  color: var(--dark);
}

button {
  padding: 0.6rem 1rem;
  border: none;
  background-color: var(--dark);
  color: white;
  font-weight: bold;
  border-radius: 6px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: var(--accent);
}

.product-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.product-card {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: white;
  padding: 0.75rem 1rem;
  border: 1px solid var(--accent);
  border-radius: 8px;
  margin-bottom: 0.75rem;
}

.card-buttons button {
  margin-left: 0.5rem;
}
</style>
