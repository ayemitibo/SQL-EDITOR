<template>
  <client-only>
    <div class="m-20">
      <div
        class="
          mb-5
          grid grid-cols-1
          rounded-lg
          bg-gray-50
          shadow
          divide-y divide-gray-200
          md:grid-cols-5
          md:divide-y-0 md:divide-x
        "
      >
        <!-- from -->
        <org-select
          v-model="getCurrentContent"
          v-bind="{
            options: allContent,
            label: 'From',
          }"
        />

        <!-- select -->

        <org-select
          v-model="selected"
          v-bind="{
            options: getHeader,
            label: 'Select',
            multiple: true,
          }"
        />
      </div>

      <!-- Table -->

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
                <caption class="hidden" />
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
                      <org-table-popover
                        :items="getContent"
                        :column="item"
                        @isWhere="where = $event"
                        @sortBy="sort = $event"
                      >
                        {{ item }}
                      </org-table-popover>
                    </th>
                  </tr>
                </thead>
                <tbody class="bg-white divide-y divide-gray-200">
                  <tr v-for="(item, index) in getFilteredContent" :key="index">
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
    </div>
  </client-only>
</template>

<script>
export default {
  async asyncData ({ $content }) {
    return {
      allContentFetched: {
        categories: await $content('categories').fetch()
        // customers: await $content('customers').fetch(),
        // employerTerritories: await $content('employee_territories').fetch(),
        // employees: await $content('employees').fetch(),
        // orderDetails: await $content('order_details').fetch(),
        // orders: await $content('orders').fetch(),
        // products: await $content('products').fetch(),
        // regions: await $content('regions').fetch(),
        // shippers: await $content('shippers').fetch(),
        // suppliers: await $content('suppliers').fetch(),
        // territories: await $content('territories').fetch()
      }
    }
  },
  data () {
    return {
      allContent: [
        'categories',
        'customers',
        'employee_territories',
        'employees',
        'order_details',
        'products',
        'regions',
        'shippers',
        'suppliers',
        'territories'
      ],
      currentContent: 'categories',
      selected: [],
      where: [],
      sort: []
    }
  },
  computed: {
    getContent () {
      const content = this.allContentFetched[this.getCurrentContent]?.body
      return content
    },
    getFilteredContent () {
      const content = this.getContent
      let filtered = content
      // where
      const isWhere = this.where && this.where.length
      if (isWhere) {
        filtered = filtered.filter(
          item => item[this.where[0]] === this.where[1]
        )
      }

      // sortBy
      if (this.sort[0] && this.sort[1]) {
        filtered.sort((a, b) => {
          if (a[this.sort[0]] < b[this.sort[0]]) {
            return -1
          }
          if (a[this.sort[0]] > b[this.sort[0]]) {
            return 1
          }
          return 0
        })
        if (this.sort[1] === 'desc') {
          filtered.reverse()
        }
      }
      return filtered
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
      async set (value) {
        this.selected = []
        this.where = []
        if (!this.allContentFetched[value]) {
          this.allContentFetched[value] = await this.$content(value).fetch()
        }
        this.currentContent = value
      }
    }
  },
  methods: {}
}
</script>
