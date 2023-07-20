<script setup>
import TabComp from './TabComp.vue'
import {reactive, computed} from 'vue'
import {useVuelidate} from '@vuelidate/core'
import{ required, email, sameAs, minLength, helpers} from '@vuelidate/validators'

const state = reactive ({
    visible:false,
    alreadyRegistered:false,
    successRegister:false,
    options:[
    {name:"Argentina", value:"ARG"},
    {name:"Brasil", value:"BRA"},
    {name:"Bolivia", value:"BOL"},
    {name:"Chile", value:"CHL"},
    {name:"Colombia", value:"COL"},
    {name:"Ecuador", value:"ECU"},
    {name:"Guyana", value:"GUY"},
    {name:"Guyana Francesa", value:"GUF"},
    {name:"Paraguay", value:"PRY"},
    {name:"Peru", value:"PER"},
    {name:"Surinam", value:"SUR"},
    {name:"Uruguay", value:"URY"},
    {name:"Venezuela", value:"VEN"},
    ],
    registeredUsers:[
    ],
    name:'',
    email:'',
    password: {
        password1:'',
        password2 :'',
    },
    terms: true,
    userNationality: '',
})

const rules = computed (() => {
    return {
        name: { required : helpers.withMessage("Name is required", required) },
        email: { required : helpers.withMessage("Email is required", required) , email: helpers.withMessage("Email must be valid", email)},
        password:{
            password1: { required, minLength: minLength(3) },
            password2: { required, sameAs: helpers.withMessage("Password must be identical", sameAs(state.password.password1))  }
        },
        terms : { requiredIf: () => state.terms },
    }
})
const v$ = useVuelidate(rules, state)

const onSubmit = async () => {
    const result = await v$.value.$validate()
    result ?  registerUser() : console.log("error - form is not valid check inputs")
}

const showInfo = () => {
    state.visible = !state.visible;
}

const registerUser = () => {
    if (state.registeredUsers.find(user => user.email === state.email)) {
        state.alreadyRegistered = true;
        state.successRegister = false;
    } else {
        state.registeredUsers.push({name: state.name, email: state.email, password: state.password.password1});
        state.alreadyRegistered = false;
        state.successRegister = true;
        state.visible = true;
        clearForm();
    }
}

const clearForm = () => {
    state.name = "";
    state.email = "";
    state.password.password1 = "";
    state.password.password2 = "";
    state.terms = true;
    state.userNationality = "";
    v$.value.$reset();
}

const updateList = (e) => {
    state.registeredUsers = e;
}

</script>

<template>
    <div>
        <form ref="registerForm" @submit.prevent="onSubmit">
            <div class="login-container">
                <div class="form-group">
                    <div class="intput-group mb-3">
                        <div class="input-grou-prepend">
                            <label name="name" for="name" class="input-group-text" >Name</label>
                        </div>
                        <input type="text" id="name" class="form-control" v-model="state.name" placeholder="enter name" required name="name">
                        <span v-if="v$.name.$error">
                            {{ v$.name.$errors[0].$message }}
                        </span>
                    </div>
                </div>
                <div class="form-group">
                    <div class="intput-group mb-3">
                        <div class="input-grou-prepend">
                            <label name="email" for="email" class="input-group-text" >Email</label>
                        </div>
                        <input type="text" id="email" class="form-control" v-model="state.email" placeholder="enter email" required name="email">
                        <span v-if="v$.email.$error">
                            {{ v$.email.$errors[0].$message }}
                        </span>
                    </div>
                </div>
                <div class="form-group">
                    <div class="intput-group mb-3">
                        <div class="input-grou-prepend">
                            <label for="password1" name="password1" class="input-group-text" >Enter password</label>
                        </div> 
                        <input  type="password" 
                        id="password1" 
                        autocomplete="section-blue your password"
                        class="form-control" 
                        v-model="state.password.password1" 
                        placeholder="enter password" 
                        required name="password1">
                        <span v-if="v$.password.password1.$error">
                            {{ v$.password.password1.$errors[0].$message }}
                        </span>
                    </div> 
                </div>
                <div class="form-group">
                    <div class="intput-group mb-3">
                        <div class="input-grou-prepend">
                            <label name="password2" for="password2" class="input-group-text" >Enter password</label>
                        </div>          
                        <input  type="password" 
                        id="password2" 
                        class="form-control" 
                        v-model="state.password.password2" 
                        autocomplete="section-blue re enter your password"
                        placeholder="re-enter password" 
                        required name="password2">
                        <span v-if="v$.password.password2.$error">
                            {{ v$.password.password2.$errors[0].$message }}
                        </span>
                    </div> 
                </div>
                <div class="form-group">
                    <div class="intput-group mb-3">
                        <div class="input-grou-prepend">
                            <label for="checkbox" name="termsconditions" class="input-group-text" >Terms and Conditions</label>
                        </div>          
                        <input type="checkbox" id="checkbox" v-model="state.terms" name="termsconditions" value="accept" /> <span> I Agree  </span>
                        <span v-if="v$.terms.$error">
                            {{ v$.terms.$errors[0].$message }}
                        </span>
                    </div> 
                </div>  
                <div v-if="state.alreadyRegistered"><span>This email is already registered</span></div>
                <div v-if="state.successRegister"><span>Success!</span></div>
                <button class="btn btn-success mt-3" type="submit" >Send</button>
                <button class="btn btn-danger mt-3"  type="button" v-on:click="clearForm()">Clear </button>
            </div>
        </form>
        <button class="btn btn-info mt-3" @click="showInfo">Show Info</button>
        
        <TabComp  v-if="state.visible"
        :users="state.registeredUsers"
        label="Nacionality"
        id="dynamic"
        :options="state.options"
        v-model="state.userNationality"
        @updateList="updateList"
        />
    </div>
</template>

<style scoped>
.login-container {
    max-width: 400px;
    margin: 0 auto;
    padding: 20px;
    border: 1px solid #ccc;
    margin-top: 1.3rem;
    border-radius: 0.45rem;
}
</style>