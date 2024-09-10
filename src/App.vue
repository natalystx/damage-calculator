<template>
  <div
    class="max-w-2xl px-4 sm:px-6 lg:px-12 py-6 sm:py-14 lg:py-20 mx-auto space-y-4"
  >
    <Input
      id="base_atk"
      label="Base attack / magic attack"
      v-model="baseAtk"
      placeholder="Enter base attack / magic attack"
      type="number"
    />

    <Input
      id="crit_damage"
      label="Critical damage (%)"
      v-model="critDamage"
      placeholder="Enter critical damage"
      type="number"
    />

    <Input
      id="base_amp"
      label="Base amplification Sword / Magic (%)"
      v-model="baseAmp"
      placeholder="Enter base amplification Sword / Magic"
      type="number"
    />

    <Input
      id="additional_atk"
      label="Additional attack / magic attack"
      v-model="additionalAtk"
      placeholder="Enter additional attack / magic attack"
      type="number"
    />

    <Input
      id="additional_crit_damage"
      label="Additional Critical damage (%)"
      v-model="additionalCritDamage"
      placeholder="Enter additional critical damage (%)"
      type="number"
    />

    <Input
      id="additional_amp"
      label="Additional amplification Sword / Magic (%)"
      v-model="additionalAmp"
      placeholder="Enter additional amplification Sword / Magic"
      type="number"
    />

    <div class="space-y-2">
      <LabelValue
        label="Total damage by critical hit"
        :value="`${Math.round(totalCriticalDamage)}`"
      />

      <LabelValue
        label="Total damage by critical hit (with additional stats)"
        :value="`${Math.round(totalDamageWithAdditionalStats)}`"
      />

      <LabelValue
        label="1% Amp attack = ATK/MATK"
        :value="`${Math.round(atkToAmpEquivalent)}`"
      />

      <LabelValue
        label="1% Amp = X% Crit Damage"
        :value="`~ ${Math.round(ampToCritDamageEquivalent)}%`"
      />
    </div>
  </div>
</template>

<script lang="ts" setup>
import Input from "./components/Input.vue";
import LabelValue from "./components/LabelValue.vue";
import { ref, computed } from "vue";

const baseAtk = ref(1000);
const critDamage = ref(100);
const baseAmp = ref(50);

const additionalAtk = ref(0);
const additionalCritDamage = ref(0);
const additionalAmp = ref(0);

const totalCriticalDamage = computed(() => {
  const amplifiedBase = baseAtk.value * (1 + baseAmp.value / 100);

  return amplifiedBase * (1 + critDamage.value / 100);
});

const totalDamageWithAdditionalStats = computed(() => {
  const totalAtk = baseAtk.value + additionalAtk.value;

  const totalAmp = baseAmp.value + additionalAmp.value;

  const totalCritDamage = critDamage.value + additionalCritDamage.value;

  const amplifiedBase = totalAtk * (1 + totalAmp / 100);
  return amplifiedBase * (1 + totalCritDamage / 100);
});

const atkToAmpEquivalent = computed(() => {
  return baseAtk.value / 100;
});

const ampToCritDamageEquivalent = computed(() => {
  const totalDamageBeforeAmp =
    baseAtk.value * (1 + baseAmp.value / 100) * (1 + critDamage.value / 100);

  const totalDamageAfterAmp =
    baseAtk.value *
    (1 + (baseAmp.value + 1) / 100) *
    (1 + critDamage.value / 100);

  const damageIncreaseFromAmp = totalDamageAfterAmp - totalDamageBeforeAmp;

  const critDamageBoost =
    (totalDamageBeforeAmp + damageIncreaseFromAmp) /
      (baseAtk.value * (1 + baseAmp.value / 100)) -
    1;

  return critDamageBoost * 100 - critDamage.value;
});
</script>
