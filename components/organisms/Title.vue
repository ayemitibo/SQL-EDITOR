<template>
  <div>
    <label
      htmlFor="email"
      class="block text-sm font-medium text-gray-700 uppercase ml-5 mt-2"
    >
      <slot />
    </label>
    <!-- DESCRIPTION -->
    <slot name="description" />
    <!-- select -->
    <slot name="select" />
  </div>
</template>
