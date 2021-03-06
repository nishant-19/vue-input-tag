<script>
/*eslint-disable*/
const validators = {
  email: new RegExp(/^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/),
  url: new RegExp(/^(https?|ftp|rmtp|mms):\/\/(([A-Z0-9][A-Z0-9_-]*)(\.[A-Z0-9][A-Z0-9_-]*)+)(:(\d+))?\/?/i),
  text: new RegExp(/^[a-zA-Z]+$/),
  digits: new RegExp(/^[\d() \.\:\-\+#]+$/),
  isodate: new RegExp(/^\d{4}[\/\-](0?[1-9]|1[012])[\/\-](0?[1-9]|[12][0-9]|3[01])$/)
}
/*eslint-enable*/

export default {
  name: 'InputTag',

  props: {
    tags: {
      type: Array,
      default: () => []
    },
    placeholder: {
      type: String,
      default: ''
    },
    readOnly: {
      type: Boolean,
      default: false
    },
    validate: {
      type: String,
      default: ''
    },
    limit: {
      default: -1
    }
  },

  data () {
    return {
      newTag: '',
      innerTags: [...this.tags]
    }
  },

  watch: {
    tags () {
      this.innerTags = [...this.tags]
    }
  },

  computed: {
    isLimit: function () {
      return this.limit > 0 && Number(this.limit) === this.innerTags.length
    }
  },

  methods: {
    focusNewTag () {
      if (this.readOnly || !this.$el.querySelector('.new-tag')) { return }
      this.$el.querySelector('.new-tag').focus()
    },

    addNew (tag) {
      if (this.isLimit) return
      if (tag && this.innerTags.indexOf(tag) === -1 && this.validateIfNeeded(tag)) {
        this.innerTags.push(tag)
        this.tagChange()
      }
      this.newTag = ''
    },

    validateIfNeeded (tagValue) {
      if (this.validate === '' || this.validate === undefined) {
        return true
      } else if (Object.keys(validators).indexOf(this.validate) > -1) {
        return validators[this.validate].test(tagValue)
      }
      return true
    },

    remove (index) {
      this.innerTags.splice(index, 1)
      this.tagChange()
    },

    removeLastTag () {
      if (this.newTag) { return }
      this.innerTags.pop()
      this.tagChange()
    },

    tagChange () {
      this.$emit('update:tags', this.innerTags)
    }
  }
}
</script>

<template>
  <div @click="focusNewTag()" :class="{'read-only': readOnly}" class="vue-input-tag-wrapper">
    <span v-for="(tag, index) in innerTags" :key="index" class="input-tag">
      <span>{{ tag }}</span>
      <a v-if="!readOnly" @click.prevent.stop="remove(index)" class="remove"></a>
    </span>
    <input
      v-if                                = "!readOnly && !isLimit"
      ref                                 = "inputtag"
      :placeholder                        = "placeholder"
      type                                = "text"
      v-model                             = "newTag"
      v-on:keydown.delete.stop            = "removeLastTag()"
      v-on:keydown.enter.188.prevent.stop = "addNew(newTag)"
      class                               = "new-tag"
    />
  </div>
</template>

<style>
.vue-input-tag-wrapper {
  background-color: #fff;
  border: 1px solid #ccc;
  overflow: hidden;
  padding-left: 4px;
  padding-top: 4px;
  cursor: text;
  text-align: left;
  -webkit-appearance: textfield;
}

.vue-input-tag-wrapper .input-tag {
  background-color: #cde69c;
  border-radius: 2px;
  border: 1px solid #a5d24a;
  color: #638421;
  display: inline-block;
  font-size: 13px;
  font-weight: 400;
  margin-bottom: 4px;
  margin-right: 4px;
  padding: 3px;
}

.vue-input-tag-wrapper .input-tag .remove {
  cursor: pointer;
  font-weight: bold;
  color: #638421;
}

.vue-input-tag-wrapper .input-tag .remove:hover {
  text-decoration: none;
}

.vue-input-tag-wrapper .input-tag .remove::before {
  content: " x";
}

.vue-input-tag-wrapper .new-tag {
  background: transparent;
  border: 0;
  color: #777;
  font-size: 13px;
  font-weight: 400;
  margin-bottom: 6px;
  margin-top: 1px;
  outline: none;
  padding: 4px;
  padding-left: 0;
}

.vue-input-tag-wrapper.read-only {
  cursor: default;
}
</style>
