<script setup>
import { statuses } from "@/const/statuses";
import { ref } from "vue";

const input = ref("");
const inputDate = ref("");
const isErrMsg = ref(false);

function onSubmitForm(e){
    e.preventDefault();

    // 入力が無い場合にはエラーメッセージを表示して処理を終了させる
    if(input.value=="" || inputDate.value==""){
        isErrMsg.value = true;
        return;
    }

    // ローカルストレージからデータ取得
    // JSON.parseでJSON形式から配列で扱えるように変換
    const items = JSON.parse(localStorage.getItem("items")) || [];

    // 保存するタスクオブジェクト
    const newItem = {
        id: items.length, //items.lengthで0から始まる連番になる
        content: input.value, //ref使っているからvalueが必要
        limit: inputDate.value,
        state: statuses.NOT_START,
        onEdit: false, //編集中かどうかのフラグ
    }

    // 取得したデータに新しいデータをpush
    items.push(newItem);

    // 配列をローカルストレージに保存
    // JSON.stringifyで配列をJSON形式に変換
    localStorage.setItem("items", JSON.stringify(items));

}
</script>


<template>
    <div>
        <p v-if="isErrMsg" class="errMsg">タスク・期限を両方入力してください。</p>
        <form @submit="onSubmitForm">
            <label for="todo">やること<input type="text" name="todo" id="todo" v-model="input"/></label>
            <label for="date">期限<input type="date" name="date" id="date" v-model="inputDate"/></label>
            <input type="submit" value="登録!" />
        </form>
    </div>
</template>
