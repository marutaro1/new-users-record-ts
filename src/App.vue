<template>
  <div>
    <div v-if="authentication">
      <div id="nav">
        <nav class="navbar navbar-expand p-0">
          <ul class="nav nav-tabs col-7 col-lg-10 mt-0">
            <li class="nav-item">
              <router-link to="/newuser" class="btn btn-primary p-1 m-1"
                >利用者登録</router-link
              >
            </li>
            <li class="nav-item">
              <router-link to="/users" class="btn btn-primary p-1 m-1"
                >利用者一覧</router-link
              >
            </li>
            <li class="nav-item">
              <router-link to="/archives" class="btn btn-primary p-1 m-1"
                >記録まとめ</router-link
              >
            </li>
            <li class="nav-item">
              <router-link to="/staff" class="btn btn-primary p-1 m-1"
                >職員関係</router-link
              >
            </li>
          </ul>
          <ul class="col-5 col-lg-2 pt-3 text-center">
            <li @click="logOut" class="btn btn-warning p-2 mx-2">ログアウト</li>
          </ul>
        </nav>
      </div>
      <router-view></router-view>
    </div>
    <div v-else-if="!idLoginView" style="margin-top: 20%">
      <h2 class="text-center text-black-50">Users-Record</h2>

      <div class="text-center">
        <img src="./assets/users-record-logo.png" class="col-2 col-lg-1 mb-1" />
      </div>

      <div class="text-center">
        <label class="col-5 col-form-label">Eメール</label>
        <div class="col-6 col-lg-3 mx-auto">
          <input
            type="text"
            v-model="email"
            class="form-control"
            placeholder="Eメール"
          />
        </div>

        <label class="col-5 col-form-label">パスワード</label>
        <div class="col-6 col-lg-3 mx-auto">
          <input
            type="text"
            v-model="password"
            class="form-control"
            placeholder="パスワード"
          />
        </div>
        <div class="col-12 mt-4 mx-auto">
          <button @click="doLogin" class="btn btn-primary col-5 col-lg-2">
            ログイン
          </button>
        </div>
        <div class="col-12 mt-4 mx-auto">
          <button @click="loginChange" class="btn btn-warning col-6 col-lg-2">
            IDログインへ切り替え
          </button>
        </div>
      </div>
    </div>

    <div v-if="idLoginView" style="margin-top: 20%">
      <h2 class="text-center text-black-50">Users-Record</h2>

      <div class="text-center">
        <img src="./assets/users-record-logo.png" class="col-2 col-lg-1 mb-1" />
      </div>
      <div class="text-center">
        <label class="col-5 col-form-label">ID</label>
        <div class="col-6 col-lg-3 mx-auto">
          <input
            type="text"
            v-model="loginId"
            class="form-control"
            placeholder="ID"
          />
        </div>

        <label class="col-5 col-form-label">パスワード</label>
        <div class="col-6 col-lg-3 mx-auto">
          <input
            type="text"
            v-model="idPassword"
            class="form-control"
            placeholder="パスワード"
          />
        </div>
        <div class="col-12 mt-4 mx-auto">
          <button @click="loginIdChange" class="btn btn-primary col-5 col-lg-2">
            ログイン
          </button>
          <div class="col-12 mt-4 mx-auto">
            <button @click="loginChange" class="btn btn-warning col-8 col-lg-2">
              Eメールログインへ切り替え
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Vue } from "vue-class-component";
import { auth } from "./firebase/firebase";
import MixinLogger from "./views/mixin";
import { Mixins } from "vue-property-decorator";

export default class app extends Mixins(MixinLogger) {
  loginChange() {
    //idloginViewを切り替え、ログイン方法を変える
    this.idLoginView = !this.idLoginView;
  }

  loginIdChange() {
    if (this.loginId !== "" && this.idPassword !== "") {
      this.email = this.loginId + "@test.com";
      this.password = this.idPassword + "pass";
    }
    this.doLogin();
  }
}
</script>
<style>
.scroll {
  height: 300px;
  overflow: hidden;
  overflow-y: scroll;
}

.scroll-user {
  height: 500px;
  overflow: hidden;
  overflow-y: scroll;
}

.space {
  white-space: pre-wrap;
  word-wrap: break-word;
}
</style>
