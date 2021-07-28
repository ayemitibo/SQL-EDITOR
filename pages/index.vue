<template>
  <div class="p-5">
    <client-only>
      <v-select v-model="getCurrentContent" :options="allContent" />
      <v-select
        v-model="selected"
        :options="getHeader"
        :multiple="true"
      />
      <div class="flex flex-col">
        <div class="-my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
          <div
            class="py-2 align-middle inline-block min-w-full sm:px-6 lg:px-8"
          >
            <div
              class="
                shadow
                overflow-hidden
                border-b border-gray-200
                sm:rounded-lg
              "
            >
              <table class="min-w-full divide-y divide-gray-200">
                <thead class="bg-gray-50">
                  <tr>
                    <th
                      v-for="(item, index) in filteredHeader"
                      :key="index"
                      scope="col"
                      class="
                        px-6
                        py-3
                        text-left text-xs
                        font-medium
                        text-gray-500
                        uppercase
                        tracking-wider
                      "
                    >
                      {{ item }}
                    </th>
                  </tr>
                </thead>
                <tbody class="bg-white divide-y divide-gray-200">
                  <tr v-for="(item, index) in getContent" :key="index">
                    <td
                      v-for="(header, index2) in filteredHeader"
                      :key="index2"
                    >
                      {{ item[header] }}
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </client-only>
  </div>
</template>

<script>
export default {
  async asyncData ({ $content }) {
    return {
      allContentFetched: {
        categories: await $content('categories').fetch(),
        customers: await $content('customers').fetch(),
        employerTerritories: await $content('employee_territories').fetch(),
        employees: await $content('employees').fetch(),
        orderDetails: await $content('order_details').fetch(),
        orders: await $content('orders').fetch(),
        products: await $content('products').fetch(),
        regions: await $content('regions').fetch(),
        shippers: await $content('shippers').fetch(),
        suppliers: await $content('suppliers').fetch(),
        territories: await $content('territories').fetch()
      }
    }
  },
  data () {
    return {
      allContent: [
        'categories',
        'customers',
        'employerTerritories',
        'employees',
        'orderDetails',
        'products',
        'regions',
        'shippers',
        'suppliers',
        'territories'
      ],
      currentContent: 'categories',
      selected: []
    }
  },
  computed: {
    getContent () {
      return this.allContentFetched[this.getCurrentContent]?.body
    },
    filteredHeader: {
      get () {
        const items =
          this.allContentFetched[this.getCurrentContent]?.body[0] &&
          Object.keys(this.allContentFetched[this.getCurrentContent]?.body[0])
        return this.selected && this.selected.length ? this.selected : items
      }
    },
    getHeader: {
      get () {
        const items =
          this.allContentFetched[this.getCurrentContent]?.body[0] &&
          Object.keys(this.allContentFetched[this.getCurrentContent]?.body[0])
        return items
      }
    },
    getCurrentContent: {
      get () {
        return this.currentContent ? this.currentContent : 'categories'
      },
      set (value) {
        this.currentContent = value
      }
    }
  },
  methods: {}
}
</script>
