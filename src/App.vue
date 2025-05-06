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

    <Input
      id="enemy_def"
      label="Enemy Defense"
      v-model="enemyDef"
      placeholder="Enter enemy defense"
      type="number"
    />

    <Input
      id="penetration"
      label="Penetration"
      v-model="penetration"
      placeholder="Enter penetration"
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

const enemyDef = ref(0);
const penetration = ref(0);

function getEffectiveAtk(atk: number): number {
  const effectiveDef = Math.max(0, enemyDef.value - penetration.value);
  return Math.max(0, atk - effectiveDef);
}

const totalCriticalDamage = computed(() => {
  const effectiveAtk = getEffectiveAtk(baseAtk.value);
  const amplifiedBase = effectiveAtk * (1 + baseAmp.value / 100);
  return amplifiedBase * (1 + critDamage.value / 100);
});

const totalDamageWithAdditionalStats = computed(() => {
  const totalAtk = baseAtk.value + additionalAtk.value;
  const totalAmp = baseAmp.value + additionalAmp.value;
  const totalCritDamage = critDamage.value + additionalCritDamage.value;

  const effectiveAtk = getEffectiveAtk(totalAtk);
  const amplifiedBase = effectiveAtk * (1 + totalAmp / 100);
  return amplifiedBase * (1 + totalCritDamage / 100);
});

const atkToAmpEquivalent = computed(() => {
  return baseAtk.value / 100;
});

const ampToCritDamageEquivalent = computed(() => {
  const effectiveAtk = getEffectiveAtk(baseAtk.value);

  const totalDamageBeforeAmp =
    effectiveAtk * (1 + baseAmp.value / 100) * (1 + critDamage.value / 100);

  const totalDamageAfterAmp =
    effectiveAtk *
    (1 + (baseAmp.value + 1) / 100) *
    (1 + critDamage.value / 100);

  const damageIncreaseFromAmp = totalDamageAfterAmp - totalDamageBeforeAmp;

  const critDamageBoost =
    (totalDamageBeforeAmp + damageIncreaseFromAmp) /
      (effectiveAtk * (1 + baseAmp.value / 100)) -
    1;

  return critDamageBoost * 100 - critDamage.value;
});
</script>
