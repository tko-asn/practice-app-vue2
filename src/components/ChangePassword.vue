<template>
  <div class="block-change-password">
    <ContentTitle title="パスワードの変更" :showMenuFunc="showMenuFunc" />
    <Content :isScroll="true">
      <div class="container-change-password">
        <!-- パスワード変更フォーム -->
        <div class="block-input">
          <label class="block-input__label" for="current_password"
            >現在のパスワード</label
          >
          <SmallInput
            type="password"
            name="current_password"
            v-model="currentPassword"
          />
        </div>
        <ValidationMessage
          class="container-change-password__validation"
          :messages="currentPasswdMessage"
          v-show="currentPasswdMessage.length"
        />
        <div class="block-input">
          <label class="block-input__label" for="new_password"
            >新しいパスワード</label
          >
          <SmallInput
            type="password"
            name="new_password"
            v-model="newPassword"
          />
        </div>
        <ValidationMessage
          class="container-change-password__validation"
          :messages="newPasswdMessage"
          v-show="newPasswdMessage.length"
        />
        <div class="block-input">
          <label class="block-input__label" for="cfm_password"
            >確認用パスワード</label
          >
          <SmallInput
            type="password"
            name="cfm_password"
            v-model="cfmPassword"
          />
        </div>
        <ValidationMessage
          class="container-change-password__validation"
          :messages="cfmPasswdMessage"
          v-show="cfmPasswdMessage.length"
        />

        <!-- ボタン -->
        <SmallButton
          btnValue="保存"
          @click="changePassword"
          :isDisabled="isDisabled"
        />
      </div>
    </Content>
  </div>
</template>


<script>
import ContentTitle from "@/components/ContentTitle";
import Content from "@/components/Content";
import ValidationMessage from "@/components/ValidationMessage";
import SmallInput from "@/components/SmallInput";
import SmallButton from "@/components/SmallButton";

export default {
  props: {
    showMenuFunc: {
      type: Function,
      default: () => {},
    },
  },
  components: {
    ValidationMessage,
    ContentTitle,
    Content,
    SmallInput,
    SmallButton,
  },
  data() {
    return {
      currentPassword: "",
      newPassword: "",
      cfmPassword: "",
      // バリデーションメッセージ
      currentPasswdMessage: [],
      newPasswdMessage: [],
      cfmPasswdMessage: [],
      isDisabled: false,
    };
  },
  methods: {
    changePassword() {
      this.isDisabled = true;

      // バリデーションメッセージの初期化
      this.currentPasswdMessage = [];
      this.newPasswdMessage = [];
      this.cfmPasswdMessage = [];

      // バリデーション
      this.validate();

      if (
        this.currentPasswdMessage.length ||
        this.newPasswdMessage.length ||
        this.cfmPasswdMessage.length
      ) {
        this.isDisabled = false;
        return;
      }

      // データはオブジェクトで送信
      this.$store
        .dispatch("auth/editAuthInfo", {
          currentPassword: this.currentPassword,
          newPassword: this.newPassword,
        })
        .then(() => {
          this.$router.push({
            // マイページへ
            name: "userView",
            params: { id: this.$store.getters["auth/userId"] },
          });
        })
        .catch((err) => {
          this.isDisabled = false;
          // 現在のパスワードが違う場合
          if (err.response.data.message === "Invalid current password") {
            this.currentPasswdMessage.push(
              "現在のパスワードが正しくありません。"
            );
          }
        });
    },
    // 各入力欄バリデーション
    validate() {
      // 入力されてない場合
      if (!this.currentPassword) {
        this.currentPasswdMessage.push("現在のパスワードを入力してください。");

        // 8文字未満の場合
      } else if (this.currentPassword.length < 8) {
        this.currentPasswdMessage.push("現在のパスワードが正しくありません。");
      }

      // 入力されてない場合
      if (!this.newPassword) {
        this.newPasswdMessage.push("新しいパスワードを入力してください。");

        // 8文字未満の場合
      } else if (this.newPassword.length < 8) {
        this.newPasswdMessage.push(
          "新しいパスワードを正しく入力してください。"
        );
      }

      // 入力されてない場合
      if (!this.cfmPassword) {
        this.cfmPasswdMessage.push("確認用パスワードを入力してください。");

        // 新しいパスワードと一致しない場合
      } else if (this.newPassword !== this.cfmPassword) {
        this.cfmPasswdMessage.push("確認用パスワードが正しくありません。");
      }
    },
  },
};
</script>

<style scoped>
/* コンテナ */
.container-change-password {
  display: flex;
  flex-direction: column;
  width: 80%;
  margin: 0 auto;
  padding: 10px 20px 20px;
}

/* ラベル要素 */
.block-input__label {
  margin-right: 20px;
  font-weight: bold;
  letter-spacing: 1px;
}

/* 各入力部分 */
.block-input {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  width: 100%;
  margin-top: 5px;
}

.container-change-password__validation {
  padding: 0;
  font-size: 0.8em;
}
</style>