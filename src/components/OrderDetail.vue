<template>
  <div class="order-detail-container">
    <router-link to="/orders" class="back-link">← Back to Orders</router-link>
    
    <div class="order-actions" v-if="orderExists">
      <button @click="completeOrder" class="btn btn-success">✓ Complete Order</button>
    </div>

    <div class="order-detail" v-if="orderExists">
      <div class="order-header-card">
        <div class="order-header">
          <div class="header-item">
            <label>Order ID</label>
            <p>{{ order.orderId }}</p>
          </div>
          <div class="header-item">
            <label>Customer ID</label>
            <p>{{ order.customerId }}</p>
          </div>
          <div class="header-item">
            <label>Status</label>
            <p><span class="status-badge" :class="'status-' + order.status">{{ order.status === 0 ? 'Pending' : 'Completed' }}</span></p>
          </div>
        </div>
      </div>

      <div class="order-items">
        <h2>Order Items</h2>
        <table class="items-table">
          <thead>
            <tr>
              <th>Product ID</th>
              <th>Product Name</th>
              <th>Quantity</th>
              <th>Price</th>
              <th>Total</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in order.items" :key="item.productId">
              <td><router-link :to="`/product/${item.productId}`">{{ item.productId }}</router-link></td>
              <td>{{ productLookup(item.productId) }}</td>
              <td>{{ item.quantity }}</td>
              <td>${{ item.price.toFixed(2) }}</td>
              <td class="total">${{ (item.quantity * item.price).toFixed(2) }}</td>
            </tr>
          </tbody>
          <tfoot>
            <tr class="total-row">
              <td colspan="4">Order Total</td>
              <td>${{ orderTotal() }}</td>
            </tr>
          </tfoot>
        </table>
      </div>
    </div>
    <div class="order-detail" v-else>
      <p class="not-found">Order not found</p>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'OrderDetail',
    props: ['orders','products'],
    emits: ['completeOrder'],
    data() {
      return {
        order: null
      }
    },
    computed: {
      orderExists() {
        return !!this.order
      }
    },
    mounted() {
      this.getOrder()
    },
    methods: {
      getOrder() {
        // get the order from the orders prop
        // if not found in the orders prop, fetch from the server
        this.order = this.orders.find(order => order.orderId === this.$route.params.id);  
        
        if (!this.order) {          
          // get the order from the makeline service
          fetch(`/makeline/order/${this.$route.params.id}`)
            .then(response => {
              if (!response.ok) {
                throw new Error('Network response was not ok');
              }
              // check if the response is empty
              if (response.status === 204) {
                return null;
              }
              return response.json();
            })
            .then(data => {
              if (data) {
                this.order = data;
              } else {
                console.log('No orders from server');
              }
            })
            .catch(error => console.error(error));
        }
        
        return this.order;
      },
      completeOrder() {
        this.$emit('completeOrder', this.order.orderId)
      },
      productLookup(id) {
        return this.products.find(product => product.id === id).name;
      },
      orderTotal() {
        let total = 0;
        this.order.items.forEach(item => {
          total += item.price * item.quantity;
        });
        return total.toFixed(2);
      }
    }
  }
</script>

<style scoped>
.order-detail-container {
  width: 100%;
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

.order-actions {
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

.btn-success {
  background-color: #28a745;
  color: white;
}

.btn-success:hover {
  background-color: #218838;
  box-shadow: 0 4px 12px rgba(40, 167, 69, 0.3);
}

.order-detail {
  background: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.order-header-card {
  background: linear-gradient(135deg, #0046BE 0%, #003fa0 100%);
  padding: 1.5rem;
  border-radius: 8px;
  margin-bottom: 2rem;
}

.order-header {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 2rem;
  color: white;
}

.header-item label {
  display: block;
  font-size: 0.85rem;
  color: #FFB81C;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 0.5rem;
}

.header-item p {
  font-size: 1.3rem;
  margin: 0;
  font-weight: 600;
}

.status-badge {
  display: inline-block;
  padding: 0.4rem 0.8rem;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: 600;
  text-transform: uppercase;
}

.status-0 {
  background-color: #fff3cd;
  color: #856404;
}

.status-1 {
  background-color: #d4edda;
  color: #155724;
}

.order-items h2 {
  font-size: 1.5rem;
  color: #0046BE;
  margin: 0 0 1.5rem 0;
}

.items-table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 1rem;
}

.items-table thead {
  background-color: #f5f5f5;
  border-bottom: 2px solid #0046BE;
}

.items-table thead th {
  padding: 1rem;
  text-align: left;
  font-weight: 600;
  color: #0046BE;
  font-size: 0.9rem;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.items-table tbody td {
  padding: 1rem;
  border-bottom: 1px solid #e0e0e0;
  color: #333;
}

.items-table tbody tr:hover {
  background-color: #f9f9f9;
}

.items-table a {
  color: #0046BE;
  text-decoration: none;
  font-weight: 600;
}

.items-table a:hover {
  text-decoration: underline;
}

.total {
  font-weight: 600;
  color: #FFB81C;
}

.total-row {
  background-color: #f5f5f5;
  font-weight: 600;
  border-top: 2px solid #0046BE;
}

.total-row td {
  padding: 1rem;
  color: #0046BE;
}

.total-row td:last-child {
  color: #FFB81C;
  font-size: 1.2rem;
}

.not-found {
  color: #666;
  font-size: 1.1rem;
  text-align: center;
  padding: 2rem;
}

@media (max-width: 768px) {
  .order-header {
    grid-template-columns: 1fr;
    gap: 1rem;
  }

  .items-table {
    font-size: 0.9rem;
  }

  .items-table thead th,
  .items-table tbody td {
    padding: 0.75rem;
  }
}
</style>