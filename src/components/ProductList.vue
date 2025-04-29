<template>
  <div>
    <h1>Produktlista</h1>

    <form @submit.prevent="addProduct">
      <input v-model="newProduct.name" placeholder="Produktnamn" required />
      <input
        v-model.number="newProduct.price"
        placeholder="Pris"
        required
        type="number"
      />
      <button type="submit">Lägg till produkt</button>
    </form>

    <ul>
      <li v-for="product in products" :key="product.id">
        {{ product.name }} - {{ product.price }} kr
        <button @click="deleteProduct(product.id)">Ta bort</button>
        <button @click="startEdit(product)">Redigera</button>
      </li>
    </ul>

    <div v-if="editProduct.id">
      <h2>Redigera produkt</h2>
      <input v-model="editProduct.name" placeholder="Nytt namn" />
      <input
        v-model.number="editProduct.price"
        placeholder="Nytt pris"
        type="number"
      />
      <button @click="updateProduct">Spara ändringar</button>
      <button @click="cancelEdit">Avbryt</button>
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
      try {
        const response = await fetch("https://localhost:7172/api/Products");
        this.products = await response.json();
      } catch (error) {
        console.error("Kunde inte hämta produkter:", error);
      }
    },

    async addProduct() {
      try {
        const response = await fetch("https://localhost:7172/api/Products", {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify(this.newProduct),
        });

        if (response.ok) {
          this.newProduct = { name: "", price: 0 };
          this.fetchProducts();
        }
      } catch (error) {
        console.error("Kunde inte lägga till produkt:", error);
      }
    },

    async deleteProduct(id) {
      try {
        const response = await fetch(
          `https://localhost:7172/api/Products/${id}`,
          {
            method: "DELETE",
          }
        );

        if (response.ok) {
          this.fetchProducts();
        }
      } catch (error) {
        console.error("Kunde inte ta bort produkt:", error);
      }
    },

    startEdit(product) {
      this.editProduct = { ...product }; // klonar objektet
    },

    async updateProduct() {
      try {
        const response = await fetch(
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

        if (response.ok) {
          this.editProduct = { id: null, name: "", price: 0 };
          this.fetchProducts();
        }
      } catch (error) {
        console.error("Kunde inte uppdatera produkt:", error);
      }
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
