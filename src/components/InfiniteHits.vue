<template>
  <div
    v-if="state"
    :class="suit()"
  >
    <slot
      :items="items"
      :results="state.results"
      :is-last-page="state.isLastPage"
      :refine="refine"
    >
      <ol :class="suit('list')">
        <li
          v-for="(item, index) in items"
          :class="suit('item')"
          :key="item.objectID"
        >
          <slot
            name="item"
            :item="item"
            :index="index"
          >objectID: {{ item.objectID }}, index: {{ index }}</slot>
        </li>
      </ol>

      <slot
        name="loadMore"
        :refine="refine"
        :page="state.results.page"
        :is-last-page="state.isLastPage"
      >
        <button
          :class="[suit('loadMore'), state.isLastPage && suit('loadMore', 'disabled')]"
          :disabled="state.isLastPage"
          @click="refine"
        >Show more results</button>
      </slot>
    </slot>
  </div>
</template>

<script>
import { createWidgetMixin } from '../mixins/widget';
import { connectInfiniteHits } from 'instantsearch.js/es/connectors';
import { createSuitMixin } from '../mixins/suit';

export default {
  name: 'AisInfiniteHits',
  mixins: [
    createWidgetMixin({ connector: connectInfiniteHits }),
    createSuitMixin({ name: 'InfiniteHits' }),
  ],
  props: {
    escapeHTML: {
      type: Boolean,
      default: true,
    },
    transformItems: {
      type: Function,
      default(items) {
        return items;
      },
    },
  },
  computed: {
    widgetParams() {
      return {
        escapeHits: this.escapeHTML,
        transformItems: this.transformItems,
      };
    },
    items() {
      // Fixes InstantSearch.js connectors API: every list
      // of things must be called `items`
      return this.state.hits;
    },
  },
  methods: {
    refine() {
      // Fixes InstantSearch.js connectors API: every function
      // that trigger a search must be called `refine`
      this.state.showMore();
    },
  },
};
</script>
