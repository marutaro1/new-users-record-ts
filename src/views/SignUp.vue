<template>
  <div class="mt-2 mx-3">
    <h3>新規職員登録</h3>
    <div>
      <label class="col-4 col-form-label">新規職員名:</label>
      <div class="col-4 col-lg-2">
        <input
          type="text"
          v-model="staffName"
          class="form-control"
          placeholder="職員名"
        />
      </div>
      <br />
      <label class="col-4 col-form-label">部署:</label>
      <div class="col-6 col-lg-2">
        <select v-model="department" class="form-select form-select-sm">
          <option value="">選択してください</option>
          <option value="caregiver">介護</option>
          <option value="nurse">看護</option>
          <option value="rehabilitation">リハビリ</option>
          <option value="studentSupport">施設管理</option>
        </select>
      </div>
      <br />
      <label class="col-4 col-form-label">役職:</label>
      <div class="col-6 col-lg-2">
        <select v-model="officialPosition" class="form-select form-select-sm">
          <option value="">役職なし</option>
          <option value="leader">リーダー</option>
          <option value="chief">主任</option>
          <option value="chiefclerk">係長</option>
          <option value="sectionchief">課長</option>
        </select>
      </div>

      <br />

      <div v-if="!changeIdEmailValue">
        <div class="mb-2">
          <button @click="changeIdEmail" class="btn btn-warning col-3 col-lg-1">
            ID登録
          </button>
        </div>
        <label class="col-4 col-form-label">Eメール:</label>
        <div class="col-7 col-lg-3">
          <input
            type="text"
            v-model="email"
            class="form-control"
            placeholder="Eメール"
          />
        </div>

        <br />

        <label class="col-4 col-form-label">パスワード:</label>
        <div class="col-7 col-lg-3 mb-3">
          <input
            type="text"
            v-model="password"
            class="form-control"
            placeholder="パスワード"
          />
        </div>
        <br />
        <button @click="signUp" class="btn btn-primary">登録</button>
      </div>

      <div v-else>
        <div class="mb-2">
          <button @click="changeIdEmail" class="btn btn-warning col-5 col-lg-1">
            Eメール登録
          </button>
        </div>
        <label class="col-4 col-form-label">ID:</label>
        <div class="col-7 col-lg-3">
          <input
            type="text"
            v-model="loginId"
            class="form-control"
            placeholder="ID"
          />
          <p>@test.com</p>
        </div>

        <label class="col-4 col-form-label">パスワード:</label>
        <div class="col-7 col-lg-3 mb-3">
          <input
            type="text"
            v-model="idPassword"
            class="form-control"
            placeholder="パスワード"
          />
          <p>pass</p>
        </div>
        <br />

        <button @click="signUpIdChange" class="btn btn-primary">登録</button>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Vue } from "vue-class-component";
import { firestore, auth } from "../firebase/firebase";
import MixinLogger from "./mixin";
import { Mixins } from "vue-property-decorator";

export default class signup extends Mixins(MixinLogger) {
  changeIdEmail() {
    this.changeIdEmailValue = !this.changeIdEmailValue;
  }

  signUpIdChange() {
    if (this.loginId !== "" && this.idPassword !== "") {
      this.email = this.loginId + "@test.com";
      this.password = this.idPassword + "pass";
    }
    this.signUp();
  }
}
</script>
