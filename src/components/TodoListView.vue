<!-- タスク表示 -->

<script setup>
// ローカルストレージからデータ取得
const items = JSON.parse(localStorage.getItem("items")) || [];
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
        <tr v-for="item in items" :key="item.id">
            <td>{{ item.id }}</td>
            <td>{{ item.content }}</td>
            <td>{{ item.limit }}</td>
            <td>{{ item.state.value }}</td>
            <td><button>編集</button></td>
            <td><button>削除</button></td>
        </tr>
    </table>
</template>
