# store-admin

This is a Vue.js app that simulates a store admin portal where users can manually process orders, and manage products. It is meant to be used in conjunction with the product-service and makeline-service.

## Running the app locally

### Prerequisites

- [Node.js](https://nodejs.org/en/download/)
- [Vue CLI Service](https://cli.vuejs.org/guide/cli-service.html)
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

### Running the app
To run the necessary services, clone the repo, open a terminal, and navigate to the repo directory.

Then run the following command:

```bash
docker compose up
```

With the services running, open a new terminal and navigate to the `store-admin` directory. Then run the following commands:

```bash
export VUE_APP_PRODUCT_SERVICE_URL=http://localhost:3002/
export VUE_APP_MAKELINE_SERVICE_URL=http://localhost:3001/

npm install
npm run serve
```

When the app is running, you should see output similar to the following:

```text
  App running at:
  - Local:   http://localhost:8081/ 
  - Network: http://192.168.0.144:8081/

  Note that the development build is not optimized.
  To create a production build, run npm run build.
```

Open a browser and navigate to `http://localhost:8081/`. You should see the store admin app running.