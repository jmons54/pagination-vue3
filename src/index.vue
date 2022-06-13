<template>
  <ul class="pagination">
    <li 
      v-if="firstLast && !isFirstPage" 
      :class="classItem">
      <a 
        @click="setPage(1)" 
        :class="classLink"
      >
        <slot name="first">
          <span class="fa-light fa-angles-left"/>
        </slot>
      </a>
    </li>
    <li 
      v-if="prevNext && !isFirstPage" 
      :class="classItem">
      <a 
        @click="setPage(page - 1)" 
        :class="classLink"
      >
        <slot name="prev">
          <span class="fa-light fa-angle-left"/>
        </slot>
      </a>
    </li>
    <li 
      class="page-item"
      v-for="value of pagesFiltered"
      :class="[classItem, {'active': page === value.page}]"
    >
      <a 
        @click="setPage(value.page)"
        class="page-link"
      >
        <span v-if="value.isBreak" v-html="breakContent"/>
        <span v-else>{{ value.page }}</span>
      </a>
    </li>
    <li 
      v-if="prevNext && !isLastPage" 
      :class="classItem">
      <a 
        @click="setPage(page + 1)" 
        :class="classLink"
      >
        <slot name="prev">
          <span class="fa-light fa-angle-right"/>
        </slot>
      </a>
    </li>
    <li 
      v-if="firstLast && !isLastPage" 
      :class="classItem">
      <a 
        @click="setPage(pages)" 
        :class="classLink"
      >
        <slot name="first">
          <span class="fa-light fa-angles-right"/>
        </slot>
      </a>
    </li>
  </ul>
</template>

<script>
export default {
  props: {
    page: Number,
    pages: Array,
    range: {
      type: Number,
      default: 3
    },
    firstLast: {
      type: Boolean,
      default: true,
    },
    prevNext: {
      type: Boolean,
      default: true,
    },
    breakContent: {
      type: String,
      default: '...',
    },
    classItem: {
      type: String,
      default: 'page-item',
    },
    classLink: {
      type: String,
      default: 'page-link',
    },
  },
  computed: {
    isFirstPage() {
      return this.page === 1;
    },
    isLastPage() {
      return this.page === this.pages;
    },
    pagesFiltered() {

      const pages = [];

      function setPage (page, isBreak) {
        pages.push({
          page,
          isBreak
        })
      }

      if (this.pages <= this.range * 3) {
        for (let i = 1; i <= this.pages; i++) {
          setPage(i)
        }
      } else {

        for (let i = 0; i < this.range; i++) {
          setPage(i + 1);
        }

        const limit = this.pages - this.range

        if (this.page >= this.range) {
          
          if (this.page >= this.range * 2) {
            setPage(Math.ceil((this.page + 1) / 2), true);
          }


          for (let i = 0; i < this.range; i++) {
            const page = this.page + i - 1
            if (page > this.range && page <= limit) {
              setPage(page);
            }
          }
        }

        if (this.page + 1 < limit) {
          setPage(this.page + Math.floor((this.pages - this.page) / 2), true);
        }

        for (let i = this.pages - this.range; i < this.pages; i++) {
          setPage(i + 1)
        }
      }

      return pages;
    }
  },
  methods: {
    setPage(page) {
      this.$emit('update:page', page);
      this.$emit('change:page', page);
    },
  },
}
</script>
