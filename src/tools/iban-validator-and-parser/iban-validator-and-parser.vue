<script setup lang="ts">
import { extractIBAN, friendlyFormatIBAN, isQRIBAN, validateIBAN } from 'ibantools';
import { getFriendlyErrors } from './iban-validator-and-parser.service';
import type { CKeyValueListItems } from '@/ui/c-key-value-list/c-key-value-list.types';

const rawIban = ref('');

const ibanInfo = computed<CKeyValueListItems>(() => {
  const iban = rawIban.value.toUpperCase().replace(/\s/g, '').replace(/-/g, '');

  if (iban === '') {
    return [];
  }

  const { valid: isIbanValid, errorCodes } = validateIBAN(iban);
  const { countryCode, bban } = extractIBAN(iban);
  const errors = getFriendlyErrors(errorCodes);

  return [

    {
      label: 'IBAN有效吗？',
      value: isIbanValid,
      showCopyButton: false,
    },
    {
      label: 'IBAN 错误',
      value: errors.length === 0 ? undefined : errors,
      hideOnNil: true,
      showCopyButton: false,
    },
    {
      label: 'IBAN是QR-IBAN吗？',
      value: isQRIBAN(iban),
      showCopyButton: false,
    },
    {
      label: '城市编码',
      value: countryCode,
    },
    {
      label: 'BBAN',
      value: bban,
    },
    {
      label: 'IBAN友好格式',
      value: friendlyFormatIBAN(iban),
    },
  ];
});

const ibanExamples = [
  'FR7630006000011234567890189',
  'DE89370400440532013000',
  'GB29NWBK60161331926819',
];
</script>

<template>
  <div>
    <c-input-text v-model:value="rawIban" placeholder="输入IBAN以检查有效性。。。" test-id="iban-input" />

    <c-key-value-list :items="ibanInfo" my-5 data-test-id="iban-info" />

    <c-card title="有效的IBAN示例">
      <div v-for="iban in ibanExamples" :key="iban">
        <c-text-copyable :value="iban" font-mono :displayed-value="friendlyFormatIBAN(iban)" />
      </div>
    </c-card>
  </div>
</template>
