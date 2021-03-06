<script lang="ts">

    import Vue from "vue"

    import NetworkService from '../services/network.service'
    import MessageService from '../services/message.service'

    import roles from '../store/modules/roles'

    export default Vue.extend({

        name: "Register",

        data() {
            return {
                busy: <boolean> false,
                registerForm: {
                    username: <string> '',
                    password: <string> '',
                    roles: <Array<string>> []
                },
                rules: {
                    username: [
                        { required: true, message: 'Please input username', trigger: 'blur' }
                    ],
                    password: [
                        { required: true, message: 'Please enter password', trigger: 'blur' }
                    ],
                    roles: [
                        { required: true, message: 'Please select roles', trigger: 'blur' }
                    ]
                },
                rolesList: roles.state.rolesList
            }
        },

        created() {
            roles.dispatchGetRoles()
        },

        methods: {
            submitForm() {

                (this.$refs['registerForm'] as any).validate()
                    .then(() => {

                        this.busy = true;

                        const form = this.registerForm;

                        NetworkService
                            .register(form.username, form.password, form.roles)
                            .then(() => this.$router.push({name: 'Users'}))
                            .catch(MessageService.showError)
                            .then(() => this.busy = false)

                    })
                    .catch(() => console.error('register form validation fail'))

            }
        }

    });

</script>

<template>

    <el-form v-loading="busy" @keyup.enter.native="submitForm" :model="registerForm" :rules="rules" ref="registerForm">

        <el-form-item prop="username">
            <el-input v-model="registerForm.username" placeholder="Username"></el-input>
        </el-form-item>

        <el-form-item prop="password">
            <el-input v-model="registerForm.password" type="password" placeholder="Password"></el-input>
        </el-form-item>

        <el-form-item prop="roles">
            <el-select v-model="registerForm.roles" multiple placeholder="Roles">
                <el-option v-for="role in rolesList" :key="role.id" :value="role.id" :label="role.rolename"></el-option>
            </el-select>
        </el-form-item>

        <el-form-item>
            <el-button type="primary" @click="submitForm">Register</el-button>
        </el-form-item>

    </el-form>

</template>

<style scoped>

    .el-form {
        width: 256px;
        margin: auto;
        text-align: center;
    }

    .el-select {
        width: 100%;
    }

    @media (max-width: 512px) {
        .el-form {
            width: 80%;
        }
    }

</style>
