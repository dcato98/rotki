<template>
  <module-not-active v-if="!anyModuleEnabled" :modules="modules" />
  <borrowing v-else />
</template>

<script lang="ts">
import { Component, Mixins } from 'vue-property-decorator';
import Borrowing from '@/components/defi/Borrowing.vue';
import ModuleNotActive from '@/components/defi/ModuleNotActive.vue';
import ModuleMixin from '@/mixins/module-mixin';
import { Module } from '@/services/session/consts';

@Component({
  components: { ModuleNotActive, Borrowing }
})
export default class DecentralizedBorrowing extends Mixins(ModuleMixin) {
  readonly modules: Module[] = [
    Module.AAVE,
    Module.COMPOUND,
    Module.MAKERDAO_VAULTS,
    Module.YEARN
  ];

  get anyModuleEnabled(): boolean {
    return this.isAnyModuleEnabled(this.modules);
  }
}
</script>
