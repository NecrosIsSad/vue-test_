<template>
  <div class="cards-wrap">
    <div class="filter row">
      <select @change="toStartCards($event)" class="filter__select custom-select" name="filter" type="text" placeholder="Категории продуктов" v-model="selected">
        <option value=''>Категории продуктов</option>
        <option value="Безрисковые">Безрисковые</option>
        <option value="Умеренный риск">Умеренный риск</option>
        <option value="Рисковые">Рисковые</option>
      </select>
      <button @click="removeFilter()" type="button" class="btn btn-outline-secondary">Сбросить фильтр</button>
    </div>
    <div class="row">
      <div class="col-12 col-md-6 col-lg-4" v-for="item in paginatedCards" :key="item._id">
        <div class="card-item d-flex flex-column">
          <div class="card-item__title">
            <h2>{{item.name}}</h2>
            <h3 v-if="item.risk_ratio == '100%'">{{item.base_assets_list.base_asset.name.full}}</h3>
          </div>
          <img src="../assets/images/product-default.svg" alt="Логотип Премьер БКС">
          <table class="table table-bordered">
            <tr>
              <td>Возможный доход</td>
              <td>{{item.expected_income.first}}</td>
            </tr>
            <tr>
              <td>Защита капитала</td>
              <td>{{item.risk_ratio}}</td>
            </tr>
            <tr>
              <td>Минимальная сумма</td>
              <td>{{item.min_sum}}</td>
            </tr>
            <tr>
              <td>Срок инвестиций</td>
              <td>{{item.period.stop_date}}</td>
            </tr>
          </table>
        </div>
      </div>
    </div>
    <div v-if="pageCount > 1" class="pagination btn-group" role="group">
      <button class="btn btn-outline-secondary" :disabled="pageNumber === 0" @click="prevPage">&laquo;</button>
      <button class="btn btn-outline-secondary" @click="pageNumber = 0">1</button>
      <button class="btn btn-outline-secondary" @click="pageNumber = 1">2</button>
      <button class="btn btn-outline-secondary" :disabled="pageNumber === 1" @click="nextPage">&raquo;</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Cards',
  data () {
    return {
      selected: '',
      filters: [
        {
          'label': 'Безрисковые',
          'risk_ratio': '100%'
        },
        {
          'label': 'Умеренный риск',
          'risk_ratio': '50%'
        },
        {
          'label': 'Рисковые',
          'risk_ratio': '0%'
        }
      ],
      pageNumber: 0,
      size: {
        type: Number,
        required: false,
        default: 6
      }
    }
  },
  computed: {
    products () {
      return this.$store.getters.PRODUCTS
    },
    income () {
      return this.products.filter((item) => {
        if (Number(item.expected_income.first) !== 0) {
          return (item.expected_income.first = Number(item.expected_income.first) + '%')
        } else {
          return (item.expected_income.first = 'не ограничен')
        }
      })
    },
    risk () {
      return this.income.filter((item) => {
        if (Number(item.risk_ratio) === 0) {
          return (item.risk_ratio = 100 + '%')
        }
        if (Number(item.risk_ratio) === 1) {
          return (item.risk_ratio = 0 + '%')
        } else {
          return (item.risk_ratio = 50 + '%')
        }
      })
    },
    price () {
      return this.risk.filter((item) => {
        let x = Number(item.min_sum)
        let n = String(x).split('').reverse().join('')
        let z = n.substring(0, 3) + ' ' + n.substring(3)
        return (item.min_sum = z.split('').reverse().join('') + ' ₽')
      })
    },
    date () {
      return this.price.filter((item) => {
        let start = item.period.start_date
        let end = item.contract.execution_date
        let result = Math.floor(this.$moment(end).diff(this.$moment(start), 'months', true))
        if (result >= 4) {
          (item.period.stop_date = result + ' месяцев')
        } else {
          (item.period.stop_date = result + ' месяца')
        }
        return (item.period.stop_date)
      })
    },
    cards () {
      let filterName = this.selected
      const filters = this.filters
      const filtered = (j) => filters.find(i => i.label === filterName && i.risk_ratio === j.risk_ratio)
      return this.date.filter((item) => {
        if (filtered(item)) {
          return item
        }
        if (filterName === '') {
          return item
        }
      })
    },
    pageCount () {
      let l = Object.keys(this.cards).length
      let s = this.size.default
      return Math.ceil(l / s)
    },
    paginatedCards () {
      const start = this.pageNumber * this.size.default
      const end = start + this.size.default
      return this.cards.slice(start, end)
    }
  },
  methods: {
    removeFilter: function () {
      this.selected = ''
    },
    toStartCards: function (event) {
      this.pageNumber = 0
    },
    nextPage: function () {
      if (this.pageNumber <= 1) {
        this.pageNumber++
      }
    },
    prevPage: function () {
      this.pageNumber--
    }
  }
}
</script>

<style scoped lang="scss">
.filter {
  width: 100%;
  margin: 0 auto 25px;

  &__select {
    margin-bottom: 10px;
    cursor: pointer;
   @media screen and (min-width: 768px) {
      width: 50%;
      margin-right: 20px;
      margin-bottom: 0;
   }
  }
}

.card-item {
  background-color: #f3f3f3;
  border-top: 5px solid #0075c9;
  border-radius: 0.25rem;
  padding: 1.25rem 1rem;
  margin-bottom: 2.5rem;
  transition: all 0.5s;

  &:active,
  &:focus,
  &:hover {
    box-shadow: 0 12px 15px 0 #d2d2d2;
  }

  h2 {
    font-size: 1rem;
    font-weight: 500;
  }

  h3 {
    font-size: 0.8rem;
  }

  &__title {
    min-height: 90px;
  }

  img {
    margin: 0 0 40px;
  }
}

.table {
  font-size: 0.75rem;
  margin: 0;
}

.pagination {
  margin: 30px 0;
  width: 30%;

  button {
    &:active {
      background-color: #6c757d;
    }
  }
}
</style>
