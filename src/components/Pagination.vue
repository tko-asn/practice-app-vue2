<template>
  <ul class="list-pagination">
    <template v-if="width >= 1025">
      <!-- 前のページ -->
      <li class="item-page">
        <a
          href=""
          :class="{
            'item-page__link': true,
            'item-page__link--direction': true,
            'item-page__link--disabled': pageNumber === 1,
            'item-page__link--end': pageNumber === 1,
          }"
          @click.prevent="paginationFunc(pageNumber - 1)"
        >
          前へ
        </a>
      </li>
      <!-- ページ番号 -->
      <li class="item-page" v-for="num in total" :key="num">
        <a
          href=""
          :class="{
            'item-page__link': true,
            'item-page__link--current': num === pageNumber,
            'item-page__link--disabled': num === pageNumber,
          }"
          @click.prevent="paginationFunc(num)"
          v-show="displayPageButton(num)"
        >
          {{ num }}
        </a>
      </li>
      <!-- 次のページ -->
      <li class="item-page">
        <a
          href=""
          :class="{
            'item-page__link': true,
            'item-page__link--direction': true,
            'item-page__link--disabled': total === pageNumber,
            'item-page__link--end': total === pageNumber,
          }"
          @click.prevent="paginationFunc(pageNumber + 1)"
        >
          次へ
        </a>
      </li>
    </template>
    <template v-else>
      <!-- ページ番号 -->
      <li class="item-page">
        <a
          href=""
          :class="{
            'item-page__link': true,
            'item-page__link--current': num === pageNumber,
            'item-page__link--disabled': num === pageNumber,
          }"
          @click.prevent="paginationFunc(num)"
          v-show="displayPageButton(num)"
          v-for="num in total"
          :key="num"
        >
          {{ num }}
        </a>
      </li>
      <li class="item-page">
        <!-- 前のページ -->
        <a
          href=""
          :class="{
            'item-page__link': true,
            'item-page__link--direction': true,
            'item-page__link--disabled': pageNumber === 1,
            'item-page__link--end': pageNumber === 1,
          }"
          @click.prevent="paginationFunc(pageNumber - 1)"
        >
          前へ
        </a>
        <!-- 次のページ -->
        <a
          href=""
          :class="{
            'item-page__link': true,
            'item-page__link--direction': true,
            'item-page__link--disabled': total === pageNumber,
            'item-page__link--end': total === pageNumber,
          }"
          @click.prevent="paginationFunc(pageNumber + 1)"
        >
          次へ
        </a>
      </li>
    </template>
  </ul>
</template>

<script>
import widthMixin from "@/mixins/widthMixin";

export default {
  props: {
    total: Number, // 総ページ数
    paginationFunc: {
      type: Function,
      default: () => {},
    },
    pageNumber: {
      type: Number,
      default: 0,
    },
  },
  mixins: [widthMixin],
  methods: {
    // ページボタンの表示・非表示
    displayPageButton(num) {
      if (this.pageNumber === 1 || this.pageNumber === 2) {
        return num <= 5;
      } else if (
        this.pageNumber === this.total ||
        this.pageNumber === this.total - 1
      ) {
        return num >= this.total - 4;
      } else if (num === this.pageNumber) {
        return true;
      } else if (num < this.pageNumber) {
        return num >= this.pageNumber - 2;
      } else if (num > this.pageNumber) {
        return num <= this.pageNumber + 2;
      }
    },
  },
};
</script>

<style scoped>
.list-pagination {
  display: flex;
  justify-content: center;
  list-style: none;
  margin: 0;
  padding: 20px 0;
}

.item-page__link {
  display: flex;
  justify-content: center;
  align-items: center;
  min-width: 30px;
  height: 40px;
  margin: 0 5px;
  border: 1px solid gray;
  box-shadow: 0 2px 2px rgb(184, 181, 181);
  border-radius: 5px;
  background: #fff;
  color: rgb(101, 102, 102);
  text-decoration: none;
}

.item-page__link--current {
  background: rgba(189, 190, 190, 0.678);
}

.item-page__link--disabled {
  pointer-events: none;
}

.item-page__link--end {
  opacity: 0.5;
}

.item-page__link--direction {
  width: 60px;
}

@media screen and (max-width: 1024px) {
  .list-pagination {
    flex-direction: column;
    align-items: center;
  }

  .item-page {
    display: flex;
    margin-bottom: 10px;
  }
}
</style>
