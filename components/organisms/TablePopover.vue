<template>
  <mol-popover>
    <template #activator>
      <slot />
    </template>
    <div class="w-[300px] px-4 py-4 bg-white rounded-sm">
      <h2 class="text-center text-black font-bold capitalize">
        <slot />
      </h2>

      <div
        class="
          flex flex-col
          items-start
          text-findox-blue
          space-y-3
          mt-5
          pb-5
          border-b
        "
      >
        <button @click="sortAsc">
          Sort A to Z
        </button>
        <button @click="sortDesc">
          Sort Z to A
        </button>
      </div>

      <form class="mt-3" @submit.prevent>
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
            v-for="(val, index) in getColumn"
            :id="index"
            :key="index"
            :item="val"
            @update:modelValue="checkRadio"
          />
          <button @click="$emit('isWhere', [])">
            clear
          </button>
        </div>

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
    sortAsc () {},
    sortDesc () {},
    checkRadio (value) {
      this.$emit('isWhere', [this.column, value])
    }
  }
}
</script>
