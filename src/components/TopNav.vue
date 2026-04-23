<template>
  <nav>
    <router-link to="/" class="logo" @click="closeNav">BEST BUY</router-link>
    <ul class="nav-links" :class="{ 'nav-links--open': isNavOpen }">
      <li><router-link to="/orders" @click="closeNav">Orders</router-link></li>
      <li><router-link to="/products" @click="closeNav">Products</router-link></li>
    </ul>
    <button class="hamburger" @click="toggleNav">
      <span class="hamburger-icon"></span>
    </button>
  </nav>
</template>

<script>
export default {
  name: 'TopNav',
  data() {
    return {
      isNavOpen: false
    }
  },
  methods: {
    toggleNav() {
      this.isNavOpen = !this.isNavOpen
    },
    closeNav() {
      this.isNavOpen = false
    }
  }
}
</script>

<style scoped>
nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: linear-gradient(90deg, #0046BE 0%, #003fa0 100%);
  color: #fff;
  padding: 0.75rem 1.5rem;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  font-size: 1.5rem;
  font-weight: 600;
  box-shadow: 0 2px 8px rgba(0, 70, 190, 0.15);
  z-index: 1000;
}

nav img {
  padding-right: 15px;
  width: 100px;
  height: auto;
  align-self: center;
}

.nav-links {
  display: flex;
  list-style: none;
  gap: 2rem;
}

.logo {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-width: 112px;
  min-height: 48px;
  background: #ffe000;
  color: #1a1a1a;
  font-family: 'Oswald', Impact, sans-serif;
  font-size: 1.45rem;
  font-weight: 600;
  letter-spacing: 0.05em;
  text-decoration: none;
  padding: 0.2rem 0.8rem;
  border-radius: 2px;
}

.logo:hover {
  text-decoration: none;
}

.nav-links li a {
  color: #fff;
  text-decoration: none;
  font-size: 1rem;
  font-weight: 500;
  padding: 0.5rem 1rem;
  border-radius: 4px;
  transition: all 0.3s ease;
}

.nav-links li a:hover {
  background-color: #FFB81C;
  color: #0046BE;
  text-decoration: none;
}

.nav-links li a.router-link-active {
  background-color: #FFB81C;
  color: #0046BE;
}

.hamburger {
  display: none;
  background: none;
  border: none;
  cursor: pointer;
  padding: 0;
  margin: 0;
}

.hamburger-icon {
  display: block;
  width: 20px;
  height: 2px;
  background-color: #fff;
  position: relative;
  top: 50%;
  transform: translateY(-50%);
}

.hamburger-icon::before,
.hamburger-icon::after {
  content: '';
  display: block;
  width: 20px;
  height: 2px;
  background-color: #fff;
  position: absolute;
  left: 0;
}

.hamburger-icon::before {
  top: -6px;
}

.hamburger-icon::after {
  bottom: -6px;
}

@media (max-width: 768px) {
  .logo {
    min-width: 96px;
    min-height: 42px;
    font-size: 1.25rem;
  }

  .nav-links {
    display: none;
    position: absolute;
    top: 100%;
    left: 0;
    right: 0;
    background-color: #003fa0;
    padding: 1rem;
    flex-direction: column;
    gap: 0;
  }

  .nav-links--open {
    display: flex;
  }

  .nav-links--open li {
    padding: 0.5rem 0;
  }

  .nav-links li a {
    display: block;
    padding: 0.75rem 1rem;
  }

  .hamburger {
    display: block;
  }
}
</style>