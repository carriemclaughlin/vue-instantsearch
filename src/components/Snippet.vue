<template>
  <span
    :class="suit()"
    v-html="innerHTML"
  />
</template>

<script>
import { getPropertyByPath } from '../util/object';
import { warn } from '../util/warn';
import { createSuitMixin } from '../mixins/suit';

export default {
  name: 'AisSnippet',
  mixins: [createSuitMixin({ name: 'Snippet' })],
  props: {
    hit: {
      type: Object,
      required: true,
    },
    attribute: {
      type: String,
      required: true,
    },
    highlightedTagName: {
      type: String,
      default: 'mark',
    },
  },
  computed: {
    innerHTML() {
      const attributeValue =
        getPropertyByPath(this.hit, `_snippetResult.${this.attribute}.value`) ||
        '';

      if (process.env.NODE_ENV !== 'production' && attributeValue === '') {
        warn(
          `The "${
            this.attribute
          }" attribute might currently not be configured to be snippeted in Algolia.` +
            'See https://www.algolia.com/doc/api-reference/api-parameters/attributesToSnippet/.'
        );
      }
      return attributeValue
        .replace(
          new RegExp('<em>', 'g'),
          `<${this.highlightedTagName} class="${this.suit('highlighted')}">`
        )
        .replace(new RegExp('</em>', 'g'), `</${this.highlightedTagName}>`);
    },
  },
};
</script>
