<script setup lang="ts">
import { useHead } from '@vueuse/head';

useHead({ title: 'About - 零叉IT工具站' });
</script>

<template>
  <c-markdown :markdown="$t('about.content')" mx-auto mt-50px max-w-600px />
</template>
