<template>
  <no-premium-placeholder v-if="!premium" :text="$t('balancer.premium')" />
  <module-not-active v-else-if="!isEnabled" :modules="modules" />
  <progress-screen v-else-if="loading">
    <template #message>
      {{ $t('balancer.loading') }}
    </template>
  </progress-screen>
  <v-container v-else>
    <active-modules :modules="modules" :class="$style.modules" />
    <balancer-balances class="mt-4" :refreshing="anyRefreshing" />
  </v-container>
</template>

<script lang="ts">
import { Component, Mixins } from 'vue-property-decorator';
import { mapActions } from 'vuex';
import BaseExternalLink from '@/components/base/BaseExternalLink.vue';
import ActiveModules from '@/components/defi/ActiveModules.vue';
import ModuleNotActive from '@/components/defi/ModuleNotActive.vue';
import ProgressScreen from '@/components/helper/ProgressScreen.vue';
import NoPremiumPlaceholder from '@/components/premium/NoPremiumPlaceholder.vue';
import ModuleMixin from '@/mixins/module-mixin';
import PremiumMixin from '@/mixins/premium-mixin';
import StatusMixin from '@/mixins/status-mixin';
import { BalancerBalances } from '@/premium/premium';
import { Module } from '@/services/session/consts';
import { Section } from '@/store/const';

@Component({
  components: {
    ActiveModules,
    BalancerBalances,
    NoPremiumPlaceholder,
    BaseExternalLink,
    ProgressScreen,
    ModuleNotActive
  },
  computed: {},
  methods: {
    ...mapActions('defi', ['fetchBalancerBalances', 'fetchBalancerEvents'])
  }
})
export default class Balancer extends Mixins(
  StatusMixin,
  ModuleMixin,
  PremiumMixin
) {
  readonly section = Section.DEFI_BALANCER_BALANCES;
  readonly secondSection = Section.DEFI_BALANCER_EVENTS;
  fetchBalancerBalances!: (refresh: boolean) => Promise<void>;
  fetchBalancerEvents!: (refresh: boolean) => Promise<void>;
  readonly modules: Module[] = [Module.BALANCER];

  get isEnabled(): boolean {
    return this.isModuleEnabled(Module.BALANCER);
  }

  async mounted() {
    await Promise.all([
      this.fetchBalancerBalances(false),
      this.fetchBalancerEvents(false)
    ]);
  }
}
</script>

<style module lang="scss">
.modules {
  display: inline-flex;
  position: absolute;
  right: 88px;
  top: 125px;
}
</style>
