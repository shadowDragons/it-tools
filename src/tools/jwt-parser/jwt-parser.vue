<script setup lang="ts">
import { decodeJwt } from './jwt-parser.service';
import { useValidation } from '@/composable/validation';
import { isNotThrowing } from '@/utils/boolean';
import { withDefaultOnError } from '@/utils/defaults';

const rawJwt = ref(
  'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c',
);

const decodedJWT = computed(() =>
  withDefaultOnError(() => decodeJwt({ jwt: rawJwt.value }), { header: [], payload: [] }),
);

const sections = [
  { key: 'header', title: 'Header' },
  { key: 'payload', title: 'Payload' },
] as const;

const validation = useValidation({
  source: rawJwt,
  rules: [
    {
      validator: value => value.length > 0 && isNotThrowing(() => decodeJwt({ jwt: rawJwt.value })),
      message: '无效 JWT',
    },
  ],
});
</script>

<template>
  <c-card>
    <c-input-text v-model:value="rawJwt" label="JWT解码" :validation="validation" placeholder="输入你的jwt token" rows="5" multiline raw-text autofocus mb-3 />

    <n-table v-if="validation.isValid">
      <tbody>
        <template v-for="section of sections" :key="section.key">
          <th colspan="2" class="table-header">
            {{ section.title }}
          </th>
          <tr v-for="{ claim, claimDescription, friendlyValue, value } in decodedJWT[section.key]" :key="claim + value">
            <td class="claims">
              <span font-bold>
                {{ claim }}
              </span>
              <span v-if="claimDescription" ml-2 op-70>
                ({{ claimDescription }})
              </span>
            </td>
            <td>
              <span>{{ value }}</span>
              <span v-if="friendlyValue" ml-2 op-70>
                ({{ friendlyValue }})
              </span>
            </td>
          </tr>
        </template>
      </tbody>
    </n-table>
  </c-card>
</template>

<style lang="less" scoped>
.table-header {
  text-align: center;
}
</style>
