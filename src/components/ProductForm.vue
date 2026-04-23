<template>
  <div class="product-form-container">
    <div class="form-header">
      <h1>{{ product.id ? 'Edit Product' : 'Add New Product' }}</h1>
      <router-link to="/products" class="back-link">← Back to Products</router-link>
    </div>

    <div v-if="showValidationErrors" class="error-alert">
      <div class="error-title">Please fix the following errors:</div>
      <ul>
        <li v-for="error in validationErrors" :key="error">{{ error }}</li>
      </ul>
    </div>

    <div class="product-form">
      <div class="form-section">
        <div class="form-group">
          <label for="product-name">Product Name *</label>
          <input id="product-name" type="text" placeholder="Enter product name" v-model="product.name" />
        </div>

        <div class="form-group">
          <label for="product-price">Price *</label>
          <input id="product-price" placeholder="0.00" v-model="product.price" type="number" step="0.01" />
        </div>

        <div class="form-group">
          <label for="product-tags">Keywords</label>
          <input id="product-tags" type="text" placeholder="e.g., electronics, gadgets" v-model="product.tags" />
        </div>

        <div class="form-group full-width">
          <label for="product-description">Description *</label>
          <textarea id="product-description" rows="6" placeholder="Enter product description" v-model="product.description"></textarea>
          <div class="ai-helper" v-show="aiCapabilities.includes('description')">
            <button @click="generateDescription" class="btn btn-secondary">✨ Generate with AI</button>
          </div>
        </div>

        <div class="form-group full-width">
          <label for="product-image">Product Image</label>
          <input type="hidden" id="product-id" v-model="product.id" />
          
          <div v-show="!aiCapabilities.includes('image')">
            <input id="product-image-text" type="text" placeholder="Enter image URL" v-model="product.image" />
          </div>

          <div v-show="aiCapabilities.includes('image')" class="image-upload-section">
            <div id="product-image-container" class="image-container" :class="{ loading: isLoadingImage }">
              <img v-if="product.image" :src="product.image" alt="Product Image" />
              <div v-if="!product.image && !isLoadingImage" class="placeholder">No image yet</div>
              <div v-if="isLoadingImage" class="overlay">{{ overlayText }}</div>
            </div>
            <button id="product-image-btn" @click="generateImage" class="btn btn-secondary">✨ Generate Image with AI</button>
          </div>
        </div>
      </div>
    </div>

    <div class="form-actions">
      <button @click="saveProduct" class="btn btn-primary btn-large">Save Product</button>
      <router-link to="/products" class="btn btn-secondary btn-large">Cancel</router-link>
    </div>
  </div>
</template>

<script>
  const aiServiceUrl = '/ai/';
  const productServiceUrl = '/product/';
  
  export default {
    name: 'ProductForm',
    props: ['products'],
    emits: ['addProductsToList','updateProductInList'],
    data() {
      return {
        product: {
          id: 0,
          name: '',
          image: '/placeholder.png',
          description: '',
          price: 0.00,
          tags: []
        },
        aiCapabilities: [],
        showValidationErrors: false,
        isLoadingImage: false,
        overlayText: ''
      }
    },
    mounted() {
      // if we're editing a product, get the product details
      if (this.$route.params.id) {
        // get the product from the products list
        const product = this.products.find(product => product.id == this.$route.params.id)
        // copy the product details into the product object
        this.product = Object.assign({}, product);
        // add empty tags if the product doesn't have any
        if (!this.product.tags) {
          this.product.tags = [];
        }
      }

      fetch(`${aiServiceUrl}health`)
        .then(response => response.json())
        .then(data => {
          if (data.status === 'ok') {
            console.log('ai service health is ok');
            this.aiCapabilities = data.capabilities;
          } else {
            console.log('ai service health is not ok');
          }
        })
        .catch(error => {
          console.log('error occured when evaluating ai service health');
          console.log(error)
        })
    },
    computed: {
      validationErrors() {
        let errors = [];
        if (this.product.name.length === 0) {
          errors.push('Please enter a value for the name field');
        }

        if (this.product.description.length === 0) {
          errors.push('Please enter a value for the description field');
        }

        if (this.product.price <= 0) {
          errors.push('Please enter a value greater than 0 for the price field');
        }

        return errors;
      }
    },
    methods: {
      generateDescription() {
        // ensure the tag has a value
        if (this.product.tags.length === 0) {
          alert('Please enter a value for the keywords field')
          return;
        }

        const intervalId = this.waitForAI();

        let requestBody = {
          name: this.product.name,
          tags: this.product.tags.split(',').map(tag => tag.trim())
        }

        console.log(requestBody);
        this.product.description = "";

        fetch(`${aiServiceUrl}generate/description`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(requestBody)
        })
          .then(response => response.json())
          .then(product => {
            this.product.description = product.description
          })
          .catch(error => {
            console.log(error)
            alert('Error occurred while generating product description')
          })
          .finally(() => {
            clearInterval(intervalId);
          })
      },
      generateImage() {
        // ensure the tag has a value
        if (this.product.description === "") {
          alert('Please enter a product description')
          return;
        }

        this.isLoadingImage = true;
        this.overlayText = 'Drawing...';

        let requestBody = {
          name: this.product.name,
          description: this.product.description
        }

        console.log(requestBody);

        fetch(`${aiServiceUrl}generate/image`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(requestBody)
        })
          .then(response => {
            return response.json();
          })
          .then(product => {
            this.overlayText = 'Downloading...';
            this.product.image = '';
            this.product.image = product.image
          })
          .catch(error => {
            console.log(error)
            alert('Error occurred while generating product image')
          })
          .finally(() => {
            this.isLoadingImage = false;
          })
      },
      waitForAI() {
        let dots = '';
        const intervalId = setInterval(() => {
          dots += '.';
          this.product.description = `Thinking${dots}`;
        }, 500);
        return intervalId;
      },
      saveProduct() {
        if (this.validationErrors.length > 0) {
          this.showValidationErrors = true;
          return;
        }

        // default to updates
        let method = 'PUT';

        // get the path of the current request
        let path = this.$route.path;
        if (path.includes('add')) {
          method = 'POST';
        }

        // ensure product.price is not wrapped in quotes
        this.product.price = parseFloat(this.product.price);

        // upsert the product
        fetch(`${productServiceUrl}`, {
          method: method,
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(this.product)
        })
          .then(response => response.json())
          .then(product => {
            alert('Product saved successfully')            
            // update or add the product to the list
            if (method === 'PUT') {
              this.$emit('updateProductInList', this.product);
            } else {
              this.$emit('addProductsToList', product);
            }
            // route to product detail
            this.$router.push(`/product/${product.id}`);
          })
          .catch(error => {
            console.log(error)
            alert('Error occurred while saving product')
          })
      }
    }
  }
</script>

<style scoped>
.product-form-container {
  width: 100%;
  max-width: 900px;
  margin: 0 auto;
  margin-top: 1rem;
}

.form-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
}

.form-header h1 {
  font-size: 2rem;
  color: #0046BE;
  margin: 0;
}

.back-link {
  color: #0046BE;
  text-decoration: none;
  font-weight: 600;
  transition: all 0.3s ease;
}

.back-link:hover {
  color: #003fa0;
}

.error-alert {
  background-color: #f8d7da;
  border: 1px solid #f5c6cb;
  border-radius: 8px;
  padding: 1rem;
  margin-bottom: 1.5rem;
  color: #721c24;
}

.error-title {
  font-weight: 600;
  margin-bottom: 0.5rem;
}

.error-alert ul {
  margin: 0;
  padding-left: 1.5rem;
}

.error-alert li {
  margin-bottom: 0.5rem;
}

.product-form {
  background: white;
  border-radius: 8px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  padding: 2rem;
  margin-bottom: 2rem;
}

.form-section {
  display: grid;
  gap: 1.5rem;
}

.form-group {
  display: flex;
  flex-direction: column;
}

.form-group.full-width {
  grid-column: 1 / -1;
}

.form-group label {
  font-weight: 600;
  color: #0046BE;
  margin-bottom: 0.5rem;
  font-size: 0.95rem;
}

.form-group input,
.form-group textarea {
  padding: 0.75rem;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-family: inherit;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.form-group input:focus,
.form-group textarea:focus {
  outline: none;
  border-color: #0046BE;
  box-shadow: 0 0 0 3px rgba(0, 70, 190, 0.1);
}

.form-group input[type="number"] {
  font-family: 'Courier New', monospace;
}

.ai-helper {
  margin-top: 1rem;
}

.image-upload-section {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.image-container {
  position: relative;
  width: 100%;
  height: 300px;
  border: 2px dashed #ddd;
  border-radius: 8px;
  overflow: hidden;
  background-color: #f9f9f9;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.image-container img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.image-container .placeholder {
  color: #999;
  font-size: 1rem;
}

.overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  opacity: 0;
  transition: opacity 0.3s ease;
  font-size: 1.2rem;
  font-weight: 600;
}

.image-container.loading .overlay {
  opacity: 1;
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
  text-align: center;
}

.btn-primary {
  background-color: #FFB81C;
  color: #0046BE;
}

.btn-primary:hover {
  background-color: #ff9e00;
  box-shadow: 0 4px 12px rgba(255, 184, 28, 0.3);
}

.btn-secondary {
  background-color: #0046BE;
  color: white;
}

.btn-secondary:hover {
  background-color: #003fa0;
  box-shadow: 0 4px 12px rgba(0, 70, 190, 0.3);
}

.btn-large {
  padding: 1rem 2rem;
  font-size: 1.1rem;
}

.form-actions {
  display: flex;
  gap: 1rem;
  justify-content: flex-start;
}

@media (max-width: 768px) {
  .form-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 1rem;
  }

  .form-header h1 {
    font-size: 1.5rem;
  }

  .product-form {
    padding: 1.5rem;
  }

  .form-actions {
    flex-direction: column;
  }

  .btn-large {
    width: 100%;
  }
}
</style>