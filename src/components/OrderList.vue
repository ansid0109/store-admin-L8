<template>
  <div class="order-list-container">
    <div class="list-header">
      <h1>Orders</h1>
    </div>

    <div class="order-list" v-if="hasOrders">
      <table class="orders-table">
        <thead>
          <tr>
            <th>Order ID</th>
            <th>Customer ID</th>
            <th>Status</th>
            <th>Total</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="order in orders" :key="order.orderId" class="order-row">
            <td><router-link :to="`/order/${order.orderId}`" class="order-link">{{ order.orderId }}</router-link></td>
            <td>{{ order.customerId }}</td>
            <td><span class="status-badge" :class="'status-' + order.status">{{ order.status === 0 ? 'Pending' : 'Completed' }}</span></td>
            <td class="total-cell">${{ orderTotal(order) }}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="no-orders" v-else>
      <p>No orders to process</p>
    </div> 
  </div>
</template>

<script>
  export default {
    name: 'OrderList',
    props: ['orders'],
    emits: ['fetchOrders', 'completeOrder'],
    computed: {
      hasOrders() {
        return this.orders.length > 0
      }
    },
    methods: {
      fetchOrders() {
        this.$emit('fetchOrders')
      },
      orderTotal(order) {
        let total = 0;
        order.items.forEach(item => {
          total += item.price * item.quantity;
        });
        return total.toFixed(2);
      }
    },
    beforeMount() {
      this.fetchOrders()
    }
  }
</script>

<style scoped>
.order-list-container {
  width: 100%;
}

.list-header {
  margin-bottom: 2rem;
  margin-top: 1rem;
}

.list-header h1 {
  font-size: 2rem;
  color: #0046BE;
  margin: 0;
}

.order-list {
  background: white;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.orders-table {
  width: 100%;
  border-collapse: collapse;
}

.orders-table thead {
  background-color: #f5f5f5;
  border-bottom: 2px solid #0046BE;
}

.orders-table thead th {
  padding: 1rem;
  text-align: left;
  font-weight: 600;
  color: #0046BE;
  font-size: 0.9rem;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.order-row {
  border-bottom: 1px solid #e0e0e0;
  transition: background-color 0.2s ease;
}

.order-row:hover {
  background-color: #f9f9f9;
}

.orders-table tbody td {
  padding: 1rem;
  color: #333;
}

.order-link {
  color: #0046BE;
  text-decoration: none;
  font-weight: 600;
}

.order-link:hover {
  text-decoration: underline;
}

.total-cell {
  font-weight: 600;
  color: #FFB81C;
  font-size: 1.1rem;
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

.no-orders {
  background: white;
  padding: 2rem;
  text-align: center;
  border-radius: 8px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.no-orders p {
  color: #666;
  font-size: 1.1rem;
  margin: 0;
}

@media (max-width: 768px) {
  .orders-table {
    font-size: 0.9rem;
  }

  .orders-table thead th,
  .orders-table tbody td {
    padding: 0.75rem;
  }
}
</style>