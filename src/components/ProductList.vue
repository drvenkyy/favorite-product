<template>
  <div>
    <div class="topnav">
      <a :class="showProductList ? 'active' : ''" @click="ProductClick(showProductList = true,favoriteProductList = false)"
        >Product List</a>
      <a :class="favoriteProductList ? 'active' : ''" @click="FavProductClick(showProductList = false,favoriteProductList = true)"
        ><span>Favorite Products</span>
        <span v-if="favoriteProducts.length>0" class="badge">{{favoriteProducts.length}}</span>
        </a>      
    </div>
    <div v-if="showProductList" class="grid-container">
      <div
        class="grid-item item-bg"
        v-for="(product, index) in fetchedProduct"
        :key="index"
        @click="toggleFavorite(product)"
        @mouseover="showAmount(product)"
        @mouseleave="hideAmount(showProductAmount = false,hoveredProductId = null)"
      >
        {{ product.product }}
        <span v-if="product.isFavorite"
          ><i class="fa fa-heart" style="font-size: 16px; color: red"></i
        ></span>
        <span class="tooltip" v-if="showProductAmount && hoveredProductId === product.id">
          <span class="tooltiptext">${{ product.price }}</span>
        </span>
      </div>
    </div>

    <div v-if="favoriteProductList" class="grid-container">
      <span class="no-list" v-if="favoriteProducts.length===0">No Product List found</span>
      <div
        class="grid-item"
        v-for="(product, index) in favoriteProducts"
        :key="index"
          @mouseover="showAmount(product)"
        @mouseleave="hideAmount(showProductAmount = false,hoveredProductId = null)"
        >
        {{ product.product }}
        <span class="tooltip" v-if="showProductAmount && hoveredProductId === product.id">
           <span class="tooltiptext">${{ product.price }}</span>
        </span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      fetchedProduct: [],
      favoriteProducts: [],
      showProductAmount: false,
      hoveredProductId: null,
      showProductList: true,
      favoriteProductList: false,
    };
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    fetchData() {
      fetch("https://neodigm.github.io/FED_Programming_Challenge/products.json")
        .then((response) => {
          response.json().then((data) => {
            this.fetchedProduct = data;
            this.fetchedProduct.forEach((item, i) => {
              item.id = i + 1;
            });
            this.loadFavoritesFromSessionStorage();
            console.log(data);
          });
        })
        .catch((err) => {
          console.error(err);
        });
    },
    saveFavoritesToSessionStorage() {
      sessionStorage.setItem(
        "favoriteProducts",
        JSON.stringify(this.favoriteProducts)
      );
        sessionStorage.setItem(
        "favoriteProductsList",
        JSON.stringify(this.fetchedProduct)
      );
    },
    loadFavoritesFromSessionStorage() {
      const favorites = sessionStorage.getItem("favoriteProducts");
      if (favorites) {
        this.favoriteProducts = JSON.parse(favorites);
      }
        const favoritesList = sessionStorage.getItem("favoriteProductsList");
      if (favoritesList) {
        this.fetchedProduct = JSON.parse(favoritesList);
      }
    },
    toggleFavorite(product) {
      console.log(product);
      product.isFavorite = !product.isFavorite;
      if (product.isFavorite) {
        this.favoriteProducts.push(product);
        this.favoriteProducts = this.favoriteProducts.filter(function (item,pos,self) {
          return self.indexOf(item) == pos;
        });
        sessionStorage.setItem(
        "favoriteProducts",
        JSON.stringify(this.favoriteProducts)
      );
      } else {
        const removeId = (plists, id) =>
        plists.filter(productlist => productlist.id !== id);
        this.favoriteProducts = removeId(this.favoriteProducts, product.id);
      }
      this.saveFavoritesToSessionStorage();
    },
    showAmount(product) {
      this.showProductAmount = true;
      this.hoveredProductId = product.id;
    },
  },
};
</script>

<style>
.topnav {
  overflow: hidden;
  background-color: #333;
}

.topnav a {
  float: left;
  color: #f2f2f2;
  text-align: center;
  padding: 14px 16px;
  text-decoration: none;
  font-size: 17px;
}

.topnav a:hover {
  background-color: #ddd;
  color: black;
}

.topnav a.active {
  background-color: #2196f3;
  color: white;
}
.grid-container {
  display: grid;
  grid-template-columns: auto auto auto;
  background-color: #2196f3;
  padding: 10px;
}
.grid-item {
  background-color: rgba(255, 255, 255, 0.8);
  border: 1px solid rgba(0, 0, 0, 0.8);
  padding: 20px;
  font-size: 30px;
  text-align: center;
}
.tooltip {
  position: relative;
  display: inline-block;
  border-bottom: 1px dotted black;
}

.no-list{
  font-size: 18px;
  font-weight: 600;
  padding: 14px 16px;
  
}

.tooltip .tooltiptext {
  visibility: visible;
  width: 120px;
  background-color: black;
  color: #fff;
  text-align: center;
  border-radius: 6px;
  padding: 5px 0;
  position: absolute;
  z-index: 1;
}
.item-bg:hover{
  background: #2196f3;
}
.badge {
  position: absolute;
  top: 50px;
  padding: 5px 10px;
  border-radius: 50%;
  background: red;
  color: white;
}


</style>
