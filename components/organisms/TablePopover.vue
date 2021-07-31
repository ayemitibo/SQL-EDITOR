<template>
  <mol-popover ref="popover">
    <template #activator>
      <slot />
    </template>
    <template #default="{ hide }">
      <div class="w-[300px] px-4 py-4 bg-white rounded-sm shadow">
        <!-- <h2 class="text-center text-black font-bold capitalize">
          <slot />
        </h2> -->
        <p class="text-base text-gray-600 mb-1">
          sort
        </p>
        <div class="flex items-start text-findox-blue mt-0 pb-5 border-b">
          <button
            class="
              text-sm
              flex
              items-center
              bg-gray-50
              hover:bg-gray-200
              mr-2
              p-2
              bg-transparent
              hover:text-gray-900
              rounded
              transition
              duration-150
              ease-in-out
            "
            @click="sort('asc')"
          >
            <span class="mr-1">Sort A to Z</span>
            <atom-icon name="asc" />
          </button>
          <button
            class="
              text-sm
              flex
              items-center
              bg-gray-50
              hover:bg-gray-200
              p-2
              bg-transparent
              hover:text-gray-900
              rounded
              transition
              duration-150
              ease-in-out
            "
            @click="sort('desc')"
          >
            <span class="mr-1">Sort Z to A</span>
            <atom-icon name="desc" />
          </button>
        </div>

        <form class="mt-3" @submit.prevent>
          <p class="text-base text-gray-600 text-xs text-center pb-2">
            Where {{ column }} === {{ checked || "All" }}
          </p>
          <div>
            <!-- <input
            v-model="filterText"
            class="w-full text-xs p-1 border rounded-none"
            type="search"
            :placeholder="`Filter ${column.text}`"
          /> -->
          </div>
          <div class="flex flex-col pb-8 border-b max-h-80 overflow-auto">
            <atom-radio
              :id="'all'"
              :item="'All'"
              @update:modelValue="() => checkRadio('all')"
            />
            <atom-radio
              v-for="(val, index) in getColumn"
              :id="index"
              :key="index"
              v-model="checked"
              :item="val"
              @update:modelValue="checkRadio"
            />
            <!-- <button @click="$emit('isWhere', [])">clear</button> -->
          </div>
          <button class="mt-2" @click="hide">
            Close
          </button>

          <!-- <div class="flex justify-between mt-3">
          <Button
            class="bg-yellow-500 text-white text-xs"
            type="submit"
            @click="clearFilter"
          >
            Clear Filter
          </Button>

          <Button class="border text-black text-xs" @click="hide">
            Close
          </Button>
        </div> -->
        </form>
      </div>
    </template>
  </mol-popover>
</template>
<script>
export default {
  props: {
    column: [String],
    items: {
      required: true
    }
  },
  data () {
    return {
      checked: ''
    }
  },
  computed: {
    getColumn () {
      const columnValuesSet = new Set()

      this.items.forEach((item) => {
        const val = item[this.column]

        columnValuesSet.add(val)
      })

      return Array.from(columnValuesSet)
    }
  },
  methods: {
    sort (value) {
      this.$emit('sortBy', [this.column, value])
    },
    checkRadio (value) {
      this.$emit('isWhere', value !== 'all' ? [this.column, value] : [])
      this.$refs.popover?.hide?.()
    }
  }
}
</script>
