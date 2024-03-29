<template>
  <div @mousemove.once="getMedicalHistoryData" class="mt-2">
    <div class="text-center">
      <p>年齢: {{ ageData }}</p>
    </div>
    <hr />
    <h4>既往歴</h4>

    <div>
      <label class="col-form-label col-5">発症日:</label>
      <div class="col-6 col-lg-3 mb-2">
        <input type="date" v-model="today" class="form-control" />
      </div>
      <div class="col-10 col-lg-6 mb-3">
        <textarea v-model="medicalHistory" class="form-control"> </textarea>
      </div>
      <button @click="addMedicalHistoryData" class="btn btn-primary">
        登録
      </button>
    </div>
    <hr />
    <div>
      <label class="col-form-label col-5">既往歴更新:</label>
      <div class="col-6 col-lg-3 mb-2">
        <input type="date" v-model="updateDay" class="form-control" />
      </div>
      <div class="col-10 col-lg-6 mb-2">
        <textarea
          v-model="updateMedicalHistory"
          class="form-control"
        ></textarea>
      </div>
    </div>
    <hr />
    <div>
      <label class="col-5 col-form-label">日付指定既往表示:</label>

      <div class="col-6 mb-2">
        <input type="date" v-model="dayKeywordFirst" class="form-control" />
        <p class="m-0 p-0">から</p>
        <input type="date" v-model="dayKeywordSecond" class="form-control" />
      </div>
      <button @click="getMonthsMedicalHistoryData" class="btn btn-primary px-1">
        {{ dayKeywordFirst }}-{{ dayKeywordSecond }}分表示
      </button>
      <button @click="creaDay" class="btn btn-primary px-1 mx-2">クリア</button>
    </div>
    <hr />

    <div v-if="!!dayKeywordFirst && !!dayKeywordSecond">
      <h5>{{ dayKeywordFirst }}から{{ dayKeywordSecond }}までの既往</h5>
    </div>
    <div v-else>
      <h5>既往歴</h5>
    </div>
    <div>
      <label class="col-5 col-form-label">病名検索:</label>
      <div class="col-6 col-lg-3 mb-2">
        <input
          type="text"
          autocomplete="on"
          list="medicalHistory"
          v-model="keyword"
          class="form-control"
        />
        <datalist id="medicalHistory">
          <option v-for="n in historyArray" :key="n">
            {{ n.value.history }}
          </option>
        </datalist>
      </div>
    </div>

    <div class="scroll">
      <div v-for="(medicalHistory, key) in historyArray" :key="key">
        <p>日付: {{ medicalHistory.value.day }}</p>
        <p>{{ medicalHistory.value.history }}</p>
        <button
          @click="updateMedicalHistoryData(medicalHistory.value.historyID)"
          class="btn btn-primary px-0 col-2 col-lg-1"
        >
          更新
        </button>
        <button
          @click="deleteMedicalHistoryData(medicalHistory.value.historyID)"
          class="btn btn-primary px-0 mx-2 col-2 col-lg-1"
        >
          削除
        </button>
        <hr />
      </div>
    </div>

    <div class="text-center">
      <p>{{ currentPage + 1 }}ページ</p>
      <div class="container">
        <nav>
          <ul class="pagination">
            <li class="page-item">
              <a @click="first" class="page-link">&laquo;</a>
            </li>
            <li class="page-item">
              <a @click="prev" class="page-link">&lt;</a>
            </li>

            <li v-for="i in displayPageRange" :key="i" class="page-item">
              <a @click="pageSelect(i)" class="page-link">{{ i }}</a>
            </li>

            <li class="page-item">
              <a @click="next" class="page-link">&gt;</a>
            </li>
            <li class="page-item">
              <a @click="last" class="page-link">&raquo;</a>
            </li>
          </ul>
        </nav>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Vue } from "vue-class-component";
import { firestore } from "../firebase/firebase";
import firebase from "../firebase/firebase";
import MixinLogger from "./mixin";
import { Mixins, Prop } from "vue-property-decorator";

export default class records extends Mixins(MixinLogger) {
  @Prop() id!: number;
  @Prop() userID!: string;
  @Prop() birthday!: string;

  medicalHistorysDb = firestore
    .collection("users")
    .doc(this.userID)
    .collection("medical-history");

  get historyArray() {
    this.sortArray(this.serchMedicalHistory);
    this.displayItems(this.dataArrays);
    return this.arrayData;
  }

  get serchMedicalHistory() {
    const historys = [] as string[];
    for (let i in this.medicalHistoryObj) {
      const history = this.medicalHistoryObj[i];
      if (history.value.history.indexOf(this.keyword) !== -1) {
        historys.push(history);
      }
    }
    return historys;
  }

  created() {
    this.getAge(this.birthday);
  }

  addMedicalHistoryData() {
    if (this.today == "" || this.medicalHistory == "") {
      return alert("日付、もしくは既往歴を入力してください。");
    }
    this.medicalHistorysDb
      .doc(String(this.$_uid))
      .set({
        day: this.today,
        searchDay: this.today.slice(0, 10), //検索用の値 YYYY-MM-DDで登録
        history: this.medicalHistory,
        historyID: this.$_uid,
      })
      .then(() => {
        this.medicalHistory = "";
        this.uidCreate();
      });
  }

  updateMedicalHistoryData(uid: string) {
    if (this.updateDay == "" || this.updateMedicalHistory == "") {
      return alert("日付、もしくは更新する既往歴を入力してください。");
    }
    this.medicalHistorysDb
      .doc(String(uid))
      .update({
        day: this.updateDay,
        searchDay: this.updateDay.slice(0, 10), //検索用の値 YYYY-MM-DDで登録
        history: this.updateMedicalHistory,
        historyID: uid,
      })
      .then(() => {
        this.updateDay = "";
        this.updateMedicalHistory = "";
        this.uidCreate();
      });
  }

  deleteMedicalHistoryData(uid: string) {
    this.medicalHistorysDb.doc(String(uid)).delete();
  }

  getMedicalHistoryData() {
    this.medicalHistorysDb.onSnapshot(
      (querySnapshot) => {
        const obj: {
          [key: string]: { value: firebase.firestore.DocumentData };
        } = {};
        querySnapshot.forEach((doc) => {
          obj[doc.id] = { value: doc.data() };
        });
        this.medicalHistoryObj = obj;
      },
      (error) => {
        console.log(error.message);
      }
    );
  }

  getMonthsMedicalHistoryData() {
    this.medicalHistorysDb
      .where("searchDay", ">=", this.dayKeywordFirst)
      .where("searchDay", "<=", this.dayKeywordSecond)
      .onSnapshot(
        (querySnapshot) => {
          const obj: {
            [key: string]: { value: firebase.firestore.DocumentData };
          } = {};
          querySnapshot.forEach((doc) => {
            obj[doc.id] = { value: doc.data() };
          });
          this.medicalHistoryObj = obj;
        },
        (error) => {
          console.log(error.message);
        }
      );
  }

  creaDay() {
    this.dayKeywordFirst = "";
    this.dayKeywordSecond = "";
    this.getMedicalHistoryData();
  }
}
</script>
