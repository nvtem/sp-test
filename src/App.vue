<template>
  <div>
    <form @submit.prevent="search">
      <div class="row">
        <label>Запрос</label>
        <input type="text" v-model="query" required>
      </div>

      <div class="row">
        <label>Мой бренд</label>
        <input type="text" v-model="brand" required>
      </div>

      <div class="row">
        <input type="submit" value="ПОИСК">
      </div>
    </form>

    <h1 v-if="lastQuery.length > 0">Рейтинг по запросу "{{ lastQuery }}"</h1>

    <ol>
      <li v-for="product of products" :class="{ highlight: isBrandsEqual(product.brand, lastBrand) }">
        {{ product.brand }}
      </li>
    </ol>
  </div>
</template>

<script>
  export default {
    name: 'App',

    data() {
      return {
        query: '',
        lastQuery: '',
        brand: '',
        lastBrand: '',
        products: [],
        interval: null
      }
    },

    methods: {
      search() {
        this.lastQuery = this.query
        this.lastBrand = this.brand

        this.fetchResults(this.lastQuery)
      },

      fetchResults(query) {
        const encodedQuery = encodeURI(query)

        fetch(`https://search.wb.ru/exactmatch/ru/common/v4/search?appType=1&couponsGeo=2,12,7,3,6,21,16&curr=rub&dest=-1221148,-140294,-1751445,-364763&emp=0&lang=ru&locale=ru&page=1&pricemarginCoeff=1.0&query=${encodedQuery}&reg=0&resultset=catalog&sort=popular&spp=0&suppressSpellcheck=false`)
            .then(r => r.json())
            .then(data => {
              if (data.data)
                this.products = data.data.products

              if (this.interval)
                clearInterval(this.interval)

              this.interval = setInterval(() => this.fetchResults(query), 1000)
            })
      },

      isBrandsEqual(brand1, brand2) {
        return brand1.toLowerCase() === brand2.toLowerCase()
      }
    }
  }
</script>

<style scoped>
  input[type=text] {
    padding: 8px;
    width: 200px;
  }

  input[type=submit] {
    padding: 8px 60px;
  }

  .row {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-top: 20px;
    max-width: 350px;
  }

  .highlight {
    color: green;
    font-size: 1.3em;
    font-weight: bolder;
  }
</style>