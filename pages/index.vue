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

        <org-title>
          <template #default>
            FROM
          </template>
          <template #select>
            <org-select
              v-model="getCurrentContent"
              v-bind="{
                options: allContent,
              }"
            />
          </template>
        </org-title>

        <!-- select -->

        <org-title>
          <template #default>
            SELECT
          </template>
          <template #select>
            <org-select
              v-model="selected"
              v-bind="{
                options: getHeader,
                label: 'Select',
                multiple: true,
              }"
            />
          </template>
        </org-title>
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
              <table class="w-full divide-y divide-gray-200 table-fixed">
                <caption class="hidden" />
                <thead class="bg-gray-50">
                  <tr>
                    <th
                      v-for="(item, index) in filteredHeader"
                      :key="index"
                      scope="col"
                      class="
                        whitespace-nowrap
                        px-7
                        py-3
                        text-left text-xs
                        font-medium
                        text-gray-500
                        uppercase
                        tracking-wider
                        text-left
                        cursor-pointer
                      "
                    >
                      <org-table-popover
                        :items="getContent"
                        :column="item"
                        @isWhere="where = $event"
                        @sortBy="sort = $event"
                      >
                        <span>
                          {{ item }}
                          <atom-icon name="filter" />
                        </span>
                      </org-table-popover>
                    </th>
                  </tr>
                </thead>
                <tbody class="bg-white divide-y divide-gray-200">
                  <tr v-for="(item, index) in getFilteredContent" :key="index">
                    <td
                      v-for="(header, index2) in filteredHeader"
                      :key="index2"
                      class="
                        text-left
                        whitespace-nowrap
                        text-sm
                        font-medium
                        text-gray-900
                        px-6
                        py-4
                        break-all
                        truncate
                      "
                    >
                      <span>{{ item[header] }}</span>
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
      return this.allContentFetched[this.getCurrentContent]?.body
    },
    getFilteredContent () {
      const content = this.getContent
      let filtered = content
      // where
      const isWhere = this.where && this.where.length
      const [column, searchedValues] = this.where
      if (isWhere) {
        if (searchedValues.length) {
          filtered = filtered.filter(item =>
            searchedValues.includes(item[column])
          )
        }
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
        return (
          this.allContentFetched[this.getCurrentContent]?.body[0] &&
          Object.keys(this.allContentFetched[this.getCurrentContent]?.body[0])
        )
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
