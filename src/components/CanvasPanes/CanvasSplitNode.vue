<script setup lang="ts">
import { SplitterGroup, SplitterPanel, SplitterResizeHandle } from 'reka-ui'

import { useEditorStore } from '@/app/editor/active-store'
import { childKey } from '@/app/editor/panes/split-tree'
import EditorCanvasPane from '@/components/CanvasPanes/EditorCanvasPane.vue'

import type { CanvasSplitNode as CanvasSplitNodeModel } from '@/app/editor/panes/types'

const { node } = defineProps<{
  node: CanvasSplitNodeModel
}>()

const store = useEditorStore()
const MIN_CANVAS_PANE_SIZE_PERCENT = 12

function handleLayout(splitId: string, sizes: number[]) {
  store.updateSplitSizes(splitId, sizes)
}
</script>

<template>
  <EditorCanvasPane v-if="node.type === 'pane'" :pane-id="node.paneId" />
  <SplitterGroup
    v-else
    :id="`canvas-split-${node.id}`"
    :direction="node.direction"
    :keyboard-resize-by="10"
    class="flex min-h-0 min-w-0 flex-1 overflow-hidden"
    @layout="(sizes) => handleLayout(node.id, sizes)"
  >
    <template v-for="(child, index) in node.children" :key="childKey(child)">
      <SplitterPanel
        :id="`canvas-split-panel-${node.id}-${childKey(child)}`"
        :order="index"
        :default-size="node.sizes[index] ?? 100 / node.children.length"
        :min-size="MIN_CANVAS_PANE_SIZE_PERCENT"
        class="flex min-h-0 min-w-0 overflow-hidden"
      >
        <CanvasSplitNode :node="child" />
      </SplitterPanel>

      <SplitterResizeHandle
        v-if="index < node.children.length - 1"
        :id="`canvas-split-handle-${node.id}-${index}`"
        :hit-area-margins="{ fine: 6, coarse: 18 }"
        :class="
          node.direction === 'horizontal'
            ? 'group relative z-20 -mx-1 w-2 cursor-col-resize'
            : 'group relative z-20 -my-1 h-2 cursor-row-resize'
        "
      >
        <div
          :class="
            node.direction === 'horizontal'
              ? 'pointer-events-none absolute inset-y-0 left-1/2 w-px -translate-x-1/2 bg-border group-data-[resize-handle-state=drag]:bg-accent'
              : 'pointer-events-none absolute inset-x-0 top-1/2 h-px -translate-y-1/2 bg-border group-data-[resize-handle-state=drag]:bg-accent'
          "
        />
      </SplitterResizeHandle>
    </template>
  </SplitterGroup>
</template>
