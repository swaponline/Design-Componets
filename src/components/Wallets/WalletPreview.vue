<template>
  <div class="wallet-preview">
    <cryptoicon :symbol="currencyName.toLowerCase()" size="46" class="wallet-preview__icon" />

    <div class="wallet-preview__text">
      <span class="wallet-preview__name">{{ name }}</span>
      <span class="wallet-preview__value">{{ currencyName }} {{ value }}</span>
    </div>
    <div class="wallet-preview__address">{{ minifyAddress(address) }}</div>
  </div>
</template>

<script>
import { minifyAddress } from '@/utils/common'

export default {
  name: 'WalletPreview',
  props: {
    currencyName: { type: String, required: true },
    name: { type: String, required: true },
    value: { type: [String, Number], required: true },
    address: { type: String, required: true }
  },
  methods: { minifyAddress }
}
</script>

<style lang="scss">
.wallet-preview {
  width: 100%;
  display: flex;
  align-items: center;
  padding: 12px 14px;

  &__icon {
    margin-right: 14px;
  }
  &__text {
    display: flex;
    align-self: stretch;
    flex-direction: column;
    flex-grow: 1;
    justify-content: space-between;
  }
  &__name,
  &__value,
  &__address {
    font-weight: 600;
    font-size: 18px;
  }
  &__value,
  &__address {
    color: $--dark-grey;
  }
  &__address {
    align-self: flex-start;
  }
}
</style>
