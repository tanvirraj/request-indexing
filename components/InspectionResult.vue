<script setup lang="ts">
import { useTimeAgo } from '~/composables/formatting'
import type { SitePage } from '~/types'

const props = defineProps<{
  value: Required<SitePage>
}>()

const root = computed(() => {
  return props.value
})

const value = computed(() => {
  return props.value?.inspectionResult
})
</script>

<template>
  <div v-if="value?.indexStatusResult" class="flex items-center gap-2">
    <UPopover mode="hover">
      <template v-if="value.indexStatusResult.verdict === 'PASS'">
        <UButton :to="value.inspectionResultLink" icon="i-heroicons-check-circle" color="green" variant="link">
          <UChip color="green" />
        </UButton>
      </template>
      <template v-else-if="value.indexStatusResult.verdict === 'NEUTRAL'">
        <UButton :to="value.inspectionResultLink" icon="i-heroicons-clock" color="gray" variant="link">
          <UChip color="gray" />
        </UButton>
      </template>
      <template v-else-if="value.indexStatusResult.verdict === 'FAIL'">
        <UButton :to="value.inspectionResultLink" icon="i-heroicons-x-circle" color="red" variant="link">
          <UChip color="red" />
        </UButton>
      </template>
      <template #panel>
        <div class="p-4">
          <div>
            <div class="text-gray-800 dark:text-gray-100 font-semibold mb-2">
              {{ value.indexStatusResult.coverageState }}
            </div>
            <div class="flex gap-3 justify-between mb-1">
              <div class="text-gray-700 dark:text-gray-200">
                Verdict
              </div>
              <div>{{ value.indexStatusResult.verdict }}</div>
            </div>
            <div class="flex gap-3 justify-between mb-1">
              <div class="text-gray-700 dark:text-gray-200">
                Robots.txt
              </div>
              <div>{{ value.indexStatusResult.robotsTxtState }}</div>
            </div>
            <div class="flex gap-3 justify-between mb-1">
              <div class="text-gray-700 dark:text-gray-200">
                Indexing
              </div>
              <div>{{ value.indexStatusResult.indexingState }}</div>
            </div>
            <div class="flex gap-3 justify-between mb-1">
              <div class="text-gray-700 dark:text-gray-200">
                Last Crawled
              </div>
              <div>{{ useTimeAgo(value.indexStatusResult.lastCrawlTime, true) }}</div>
            </div>
          </div>
          <slot />
        </div>
      </template>
    </UPopover>
    <div>
      <div v-if="root.lastInspected" class="text-xs mb-1">
        Inspected {{ useTimeAgo(root.lastInspected, true) }}
      </div>
      <UPopover v-if="value.indexStatusResult.verdict === 'NEUTRAL' && !root.urlNotificationMetadata?.latestUpdate" mode="hover">
        <div class="flex items-center gap-1">
          <UIcon name="i-heroicons-question-mark-circle" size="w-5 h-5" />
          <div class="text-xs">
            Why am I seeing this?
          </div>
        </div>
        <template #panel>
          <div class="p-4 text-sm space-y-2">
            <div>Google knows about your URL but has not indexed it yet.<br>Click the Request Indexing button to move it along.</div>
          </div>
        </template>
      </UPopover>
      <UPopover v-else-if="value.indexStatusResult.verdict === 'NEUTRAL'" mode="hover">
        <div class="flex items-center gap-1">
          <UIcon name="i-heroicons-question-mark-circle" size="w-5 h-5" />
          <div class="text-xs">
            Why am I seeing this?
          </div>
        </div>
        <template #panel>
          <div class="p-4 text-sm space-y-2">
            <div>You have submitted a Request Index request.<br>We're waiting for Google to process it.</div>
          </div>
        </template>
      </UPopover>
      <UPopover v-if="value.indexStatusResult.verdict === 'PASS'" mode="hover">
        <div class="flex items-center gap-1">
          <UIcon name="i-heroicons-question-mark-circle" size="w-5 h-5" />
          <div class="text-xs">
            Why am I seeing this?
          </div>
        </div>
        <template #panel>
          <div class="p-4 text-sm space-y-2">
            <div>Google has reported that this URL has been indexed. Congrats! <br>But it doesn't mean people can find it just yet.</div>
            <div>We'll track this URL here until it has appeared on a<br> search page at least once, check back later.</div>
          </div>
        </template>
      </UPopover>
    </div>
  </div>
</template>
