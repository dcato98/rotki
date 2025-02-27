<template>
  <no-premium-placeholder v-if="!premium" :text="$t('sushiswap.premium')" />
  <module-not-active v-else-if="!isEnabled" :modules="modules" />
  <progress-screen v-else-if="loading">
    <template #message>
      {{ $t('sushiswap.loading') }}
    </template>
  </progress-screen>
  <v-container v-else>
    <active-modules :modules="modules" :class="$style.modules" />
    <sushi
      class="mt-4"
      :refreshing="anyRefreshing"
      :secondary-loading="secondaryRefreshing"
    />
  </v-container>
</template>

<script lang="ts">
import { defineComponent } from '@vue/composition-api';
import { mapActions } from 'vuex';
import ActiveModules from '@/components/defi/ActiveModules.vue';
import ModuleNotActive from '@/components/defi/ModuleNotActive.vue';
import ProgressScreen from '@/components/helper/ProgressScreen.vue';
import NoPremiumPlaceholder from '@/components/premium/NoPremiumPlaceholder.vue';
import ModuleMixin from '@/mixins/module-mixin';
import PremiumMixin from '@/mixins/premium-mixin';
import StatusMixin from '@/mixins/status-mixin';
import { Sushi } from '@/premium/premium';
import { Module } from '@/services/session/consts';
import { Section } from '@/store/const';

export default defineComponent({
  name: 'Sushiswap',
  components: {
    ActiveModules,
    Sushi,
    NoPremiumPlaceholder,
    ProgressScreen,
    ModuleNotActive
  },
  mixins: [StatusMixin, ModuleMixin, PremiumMixin],
  data() {
    const section = Section.DEFI_SUSHISWAP_BALANCES;
    const secondSection = Section.DEFI_SUSHISWAP_EVENTS;
    const modules: Module[] = [Module.SUSHISWAP];
    return {
      section,
      secondSection,
      modules
    };
  },
  computed: {
    isEnabled(): boolean {
      const mixin = (this as any) as ModuleMixin;
      return mixin.isModuleEnabled(Module.SUSHISWAP);
    }
  },
  async mounted() {
    await Promise.all([this.fetchBalances(false), this.fetchEvents(false)]);
  },
  methods: {
    ...mapActions('defi/sushiswap', ['fetchBalances', 'fetchEvents'])
  }
});
</script>

<style module lang="scss">
.modules {
  display: inline-flex;
  position: absolute;
  right: 88px;
  top: 125px;
}
</style>
