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
let errMsg = ref("");
let isOnEditOther = false;
const today = new Date();

// 編集
// 初期値変数にそれぞれ今の値を代入
function onEdit(id){

    // 他に編集モードのタスクがないか調べる
    items.value.map((item) => {
        if(item.onEdit){
            isOnEditOther = true;
            return;
        }
    });

    if(isOnEditOther){
        errMsg.value = "他に編集中のタスクがあります。";
        isErrMsg.value = true;
        return;
    }

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
    errMsg.value = "タスク・期限を両方入力してください。";
    isErrMsg.value = true;
    return;
}

// splice: items配列のうち該当の要素を作ったnewItemに置き換える
items.value.splice(id, 1, newItem);
localStorage.setItem("items", JSON.stringify(items.value));
isErrMsg.value = false;

}

let deleteItemId = '';
let deleteItemContent = ref();

// 削除(モーダルウィンドウ表示)
function showDeleteModal(id){
    isShowModal.value = true;
    deleteItemId = id;
    deleteItemContent = items.value[id].content;
}


// はい（モーダルウィンドウ）
function onDeleteItem(){
    items.value.splice(deleteItemId, 1); //対象のタスクを削除
    isShowModal.value = false;

    // IDを振り直す
    items.value = items.value.map((item, index) => ({
        id: index,
        content: item.content,
        limit: item.limit,
        state: item.state,
        onEdit: item.onEdit,
    }))
    localStorage.setItem("items", JSON,stringify(items.value));
    /**
     * このあとに isShowModal.value = false;を書いてはいけない理由
     * ⇒localStorageはブラウザに保存のため、ページをリロードした後に反映される
     * ⇒falseにする前にリロードが走って実行されない
     *  */ 

}

// キャンセル（モーダルウィンドウ）
function onHideModal(){
    isShowModal.value = false;
}



</script>

<template>
    <p v-if="isErrMsg" class="errMsg">{{ errMsg }}</p>
    <table>
        <tr>
            <th class="th-id">ID</th>
            <th class="th-value">やること</th>
            <th class="th-limit">期限</th>
            <th class="th-state">状態</th>
            <th class="th-edit">編集</th>
            <th class="th-delete">削除</th>
        </tr>
    
    <!-- :class: newDate(item.limit) < todayがtrueならredというクラスを付与する -->
        <tr 
        v-for="item in items" 
        :key="item.id"
        :class="{ red: new Date(item.limit) < today}"
        >
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
            <td><button @click="showDeleteModal(item.id)">削除</button></td>
        </tr>
    </table>

    <!-- 削除時に表示するモーダルウィンドウ -->
    <div v-if="isShowModal" class="modal">
        <div class="modal-content">
            <p>{{ deleteItemContent }}を削除してもよろしいですか？</p>
            <button @click="onDeleteItem()">はい</button>
            <button @click="onHideModal()">キャンセル</button>
        </div>

    </div>
</template>

<style>
.modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
}

.modal-content{
    background-color: #fff;
    padding: 20px;
    border-radius: 8px;
}

.red{
    color: red;
}
</style>
