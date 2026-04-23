<template>
  <div class="product-list-container">
    <div class="list-header">
      <h1>Products</h1>
      <router-link to="/product/add">
        <button class="btn btn-primary">+ Add Product</button>
      </router-link>
    </div>
    
    <div class="products-grid">
      <div v-for="product in products" :key="product.productId" class="product-card">
        <router-link :to="`/product/${product.id}`" class="product-link">
          <div class="product-card-content">
            <h3>{{ product.name }}</h3>
            <p class="product-desc">{{ product.description }}</p>
            <p class="product-id">ID: {{ product.id }}</p>
            <p class="product-price">${{ parseFloat(product.price).toFixed(2) }}</p>
          </div>
        </router-link>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'ProductList',
    props: ['products'],
    mounted() {
      this.$emit('getProducts')
    }
  }
</script>

<style scoped>
.product-list-container {
  width: 100%;
}

.list-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
  margin-top: 1rem;
}

.list-header h1 {
  font-size: 2rem;
  color: #0046BE;
  margin: 0;
}

.btn {
  padding: 0.75rem 1.5rem;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  text-decoration: none;
  display: inline-block;
}

.btn-primary {
  background-color: #FFB81C;
  color: #0046BE;
}

.btn-primary:hover {
  background-color: #ff9e00;
  box-shadow: 0 4px 12px rgba(255, 184, 28, 0.3);
}

.products-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 1.5rem;
}

.product-card {
  background: white;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
  height: 100%;
}

.product-card:hover {
  box-shadow: 0 8px 16px rgba(0, 70, 190, 0.15);
  transform: translateY(-4px);
}

.product-link {
  display: block;
  color: inherit;
  text-decoration: none;
  height: 100%;
}

.product-card-content {
  padding: 1.5rem;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.product-card h3 {
  font-size: 1.1rem;
  color: #0046BE;
  margin: 0 0 0.5rem 0;
  line-height: 1.3;
}

.product-desc {
  font-size: 0.9rem;
  color: #666;
  margin: 0 0 1rem 0;
  flex-grow: 1;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.product-id {
  font-size: 0.8rem;
  color: #999;
  margin: 0.5rem 0;
}

.product-price {
  font-size: 1.3rem;
  font-weight: 700;
  color: #FFB81C;
  margin: 0;
  margin-top: auto;
}

@media (max-width: 768px) {
  .list-header {
    flex-direction: column;
    gap: 1rem;
    align-items: flex-start;
  }

  .products-grid {
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 1rem;
  }
}
</style>