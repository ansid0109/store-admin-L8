<template>
  <div class="product-detail-container">
    <router-link to="/products" class="back-link">← Back to Products</router-link>
    
    <div class="product-actions">
      <router-link :to="`/product/${$route.params.id}/edit`">
        <button class="btn btn-primary">Edit Product</button>
      </router-link>
    </div>

    <div class="product-detail" v-if="productExists">
      <div class="product-image">
        <img :src="product.image" alt="Product Image">
      </div>
      <div class="product-info">
        <h1>{{ product.name }}</h1>
        <p class="product-id">Product ID: {{ product.id }}</p>
        <p class="product-price">${{ parseFloat(product.price).toFixed(2) }}</p>
        <div class="product-description">
          <h3>Description</h3>
          <p>{{ product.description }}</p>
        </div>
      </div>
    </div>
    <div class="product-detail" v-else>
      <p class="not-found">Product not found</p>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'ProductDetail',
    props: ['products'],
    computed: {
      product() {
        return this.products.find(product => product.id == this.$route.params.id)
      },
      productExists() {
        return !!this.product
      }
    },
  }
</script>

<style scoped>
.product-detail-container {
  width: 100%;
  margin-top: 1rem;
}

.back-link {
  display: inline-block;
  margin-bottom: 1.5rem;
  color: #0046BE;
  text-decoration: none;
  font-weight: 600;
  transition: all 0.3s ease;
}

.back-link:hover {
  color: #003fa0;
}

.product-actions {
  margin-bottom: 2rem;
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

.product-detail {
  display: flex;
  gap: 3rem;
  background: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.product-image {
  flex: 0 0 40%;
}

.product-image img {
  width: 100%;
  height: auto;
  border-radius: 8px;
  background-color: #f5f5f5;
  padding: 1rem;
}

.product-info {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.product-info h1 {
  font-size: 2rem;
  color: #0046BE;
  margin: 0 0 1rem 0;
  line-height: 1.3;
}

.product-id {
  font-size: 0.9rem;
  color: #999;
  margin: 0 0 1rem 0;
}

.product-price {
  font-size: 1.8rem;
  font-weight: 700;
  color: #FFB81C;
  margin: 0 0 1.5rem 0;
}

.product-description h3 {
  font-size: 1.2rem;
  color: #0046BE;
  margin-bottom: 0.5rem;
}

.product-description p {
  color: #666;
  line-height: 1.6;
  margin: 0;
}

.not-found {
  color: #666;
  font-size: 1.1rem;
  text-align: center;
  padding: 2rem;
}

@media (max-width: 768px) {
  .product-detail {
    flex-direction: column;
    gap: 1.5rem;
  }

  .product-image {
    flex: 1;
  }

  .product-info h1 {
    font-size: 1.5rem;
  }

  .product-price {
    font-size: 1.4rem;
  }
}
</style>