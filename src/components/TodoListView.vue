<!-- タスク表示 -->

<script setup>
import { statuses } from '@/const/statuses';
import { ref } from 'vue';

// ローカルストレージからデータ取得
// refで宣言しないと、変数の値が変わっても画面に反映されない
let items =ref(JSON.parse(localStorage.getItem("items")) || []);
let inputContent = ref("");
let inputLimit = ref("");
let inputState = ref("");
let isErrMsg = ref(false);
let isShowModal = ref(false);

// 編集
function onEdit(id){
    // 初期値変数にそれぞれ今の値を代入
    inputContent.value = items.value[id].content;
    inputLimit.value = items.value[id].limit;
    inputState.value = items.value[id].state;

    items.value[id].onEdit = true; //HTMLの方はvalue不要
}

// 編集完了（保存）
function onUpdate(id){
const newItem = {
    id: id,
    content: inputContent.value,
    limit: inputLimit.value,
    state: inputState.value,
    onEdit: false,
}

// 入力が空だったらisErrMsgをtrueにする
if(inputContent.value == "" || inputLimit.value == ""){
    isErrMsg.value = true;
    return;
}

// splice: items配列のうち該当の要素を作ったnewItemに置き換える
items.value.splice(id, 1, newItem);
localStorage.setItem("items", JSON.stringify(items.value));
isErrMsg.value = false;

}


</script>

<template>
    <table>
        <tr>
            <th class="th-id">ID</th>
            <th class="th-value">やること</th>
            <th class="th-limit">期限</th>
            <th class="th-state">状態</th>
            <th class="th-edit">編集</th>
            <th class="th-delete">削除</th>
        </tr>
        <p v-if="isErrMsg" class="errMsg">タスク・期限を両方入力してください。</p>
        <tr v-for="item in items" :key="item.id">
            <td>{{ item.id }}</td>
            <td>
                <!-- onEditがfalseならタスク表示、trueなら入力欄を表示 -->
                <!-- v-model: 初期値 -->
                <span v-if="!item.onEdit">{{ item.content }}</span>
                <input v-else type="text" v-model="inputContent" name="todo" id="todo">
            </td>
            <td>
                <span v-if="!item.onEdit">{{ item.limit }}</span>
                <input v-else v-model="inputLimit" type="date" name="date" id="date">
            </td>
            <td>
                <span v-if="!item.onEdit">{{ item.state.value }}</span>
                <select v-else v-model="inputState">
                    <option 
                    v-for="state in statuses"
                    :key="state.id"
                    :value="state"
                    :selected="state.id == item.state.id"
                    >
                    {{ state.value }}
                    </option>
                </select>
                
            </td>
            <td>
                <button v-if="!item.onEdit" @click="onEdit(item.id)">編集</button>
                <button v-else @click="onUpdate(item.id)">完了</button>
            </td>
            <td><button>削除</button></td>
        </tr>
    </table>

    <!-- 削除時に表示するモーダルウィンドウ -->
    <div v-if="isShowModal" class="modal">
        <div class="modal-content">
            <p>削除してもよろしいですか？</p>
            <button>はい</button>
            <button>キャンセル</button>
        </div>

    </div>
</template>
