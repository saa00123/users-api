<template>
    <div>
        <h1>User List</h1>
        <button @click="toggleCreateForm" class="toggle-button">
            Create User
        </button>
        <table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="user in users" :key="user.id">
                    <td>{{ user.id }}</td>
                    <td>{{ user.name }}</td>
                    <td>{{ user.email }}</td>
                    <td>
                        <button
                            @click="prepareUpdate(user)"
                            class="toggle-button"
                        >
                            Update
                        </button>
                        <button
                            @click="deleteUser(user.id)"
                            class="toggle-button"
                        >
                            Delete
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>
        <div v-if="isCreateVisible">
            <CreateUser @user-created="handleUserCreated" />
        </div>
        <div v-if="isUpdateVisible">
            <UpdateUser
                :user="selectedUser"
                @user-updated="handleUserUpdated"
            />
        </div>
    </div>
</template>

<script>
import { toRefs, ref, onMounted, reactive } from "vue";
import axios from "axios";
import CreateUser from "./CreatUser.vue";
import UpdateUser from "./UpdateUser.vue";

export default {
    components: {
        CreateUser,
        UpdateUser,
    },
    setup() {
        const users = ref([]);
        const selectedUser = ref(null);

        const status = reactive({
            isCreateVisible: false,
            isUpdateVisible: false,
        });

        const loadUsers = async () => {
            try {
                const response = await axios.get("/api/users");
                users.value = response.data;
            } catch (error) {
                console.error("Error loading users:", error);
            }
        };

        const toggleCreateForm = () => {
            status.isCreateVisible = !status.isCreateVisible;
        };

        const prepareUpdate = (user) => {
            selectedUser.value = user;
            status.isUpdateVisible = true;
        };

        const handleUserCreated = () => {
            loadUsers();
            status.isCreateVisible = false;
        };

        const handleUserUpdated = () => {
            loadUsers();
            status.isUpdateVisible = false;
        };

        const deleteUser = async (userId) => {
            try {
                await axios.delete(`/api/users/${userId}`);
                loadUsers();
            } catch (error) {
                console.error("Error deleting user:", error);
            }
        };

        onMounted(loadUsers);

        return {
            users,
            selectedUser,
            loadUsers,
            toggleCreateForm,
            prepareUpdate,
            handleUserCreated,
            handleUserUpdated,
            deleteUser,
            ...toRefs(status),
        };
    },
};
</script>

<style>
table {
    width: 100%;
    border-collapse: collapse;
}

th,
td {
    border: 1px solid #ddd;
    text-align: left;
    padding: 8px;
}

th {
    background-color: #f3f3f3;
}

tr:nth-child(even) {
    background-color: #f9f9f9;
}

tr:hover {
    background-color: #ddd;
}

.toggle-button {
    padding: 10px 20px;
    background-color: #4caf50;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    outline: none;
    margin-bottom: 10px;
    margin-right: 10px;
}

.toggle-button:hover {
    background-color: #45a049;
}

.form-container {
    position: relative;
    width: 45%;
    margin-top: 20px;
    padding: 15px;
    border: 1px solid #ddd;
    border-radius: 8px;
    background-color: #f9f9f9;
}

.user-form {
    display: grid;
    grid-template-columns: 1fr 2fr;
    grid-gap: 10px;
    align-items: center;
}

.form-field {
    display: contents;
}

label {
    text-align: right;
    padding-right: 10px;
}

input[type="text"],
input[type="email"],
input[type="password"] {
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
}

.submit-button {
    grid-column: 1 / span 2;
    padding: 10px;
    background-color: #4caf50;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

.submit-button:hover {
    background-color: #45a049;
}

.error-input {
    border-color: red;
}

.error-message {
    color: red;
    margin-top: 5px;
}

.close-button {
    position: absolute;
    top: 10px;
    right: 10px;
    border: none;
    background: none;
    cursor: pointer;
    font-size: 20px;
}

.close-button:hover {
    color: #888;
}
</style>
