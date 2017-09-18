<template>
  <div>
    <div class="nav-breadcrumb-wrap">
      <div class="container">
        <nav class="nav-breadcrumb">
          <router-link href="javascript:void(0)" to="/">Home</router-link>
          <slot name='breads'></slot>
        </nav>
      </div>
    </div>
  </div>
</template>
