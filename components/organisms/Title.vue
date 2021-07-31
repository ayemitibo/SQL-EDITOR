<template>
  <div>
    <label
      htmlFor="email"
      className="block text-sm font-medium text-gray-700 uppercase"
    >
      <slot />
    </label>
    <!-- DESCRIPTION -->
    <slot name="description" />
    <!-- select -->
    <slot name="select" />
  </div>
</template>
