<template>
  <!-- <form @submit.prevent="onsubmit" class="login"> -->
  <div>
    <div class="row mb-2">
      <vue-form-generator
        :model="model"
        :schema="schema"
        :options="formOptions"
        ref="recoverPasswordForm"
      >
      </vue-form-generator>
    </div>
    <div class="row justify--center">
      <va-button v-on:click="changePass" class="my-0"
        >Change Password</va-button
      >
    </div>
  </div>
  <!-- </form> -->
</template>

<script>
import * as services02module2 from '../../../app/module2/services02'
import * as services06Module1 from "../../../app/module1/services06";
import * as services06Module0 from "../../../app/module0/services06";
export default {
  // name: "recover-password",
  // props: {
  //   USER_ID: String
  // },
  mounted() {
   // this.model.USER_ID = localStorage.getItem("recover-password");
    this.securityQuestionList();
    this.getPasswordSetting();
  },
  data() {
    return {
      maxLength: 5,
      email: "",
      securityQuestion: [],
      passwordSetting: "",
      schema: {
        groups: [
          {
            styleClasses: "row",
            fields: [
              // {
              //   type: "input",
              //   inputType: "text",
              //   label: "User ID'",
              //   model: "USER_ID",
              //   placeholder: "Please enter User ID",
              //   required: true,
              //   validator: ["string", this.validateUserId],
              //   styleClasses: "col-md-12",
              // },
              {
                type: "input",
                inputType: "password",
                label: "New Password",
                model: "USER_PASSWORD",
                placeholder: "Please enter password",
                required: true,
                validator: ["string", this.validatePassword],
                styleClasses: "col-md-12",
              },
              {
                type: "input",
                inputType: "password",
                label: "Confirm Password",
                model: "CONFIRM_PASSWORD",
                placeholder: "Confirm password",
                required: true,
                styleClasses: "col-md-12",
                validator: ["string", this.isEqualTo],
              },
              // {
              //   labels: "Mobile Phone Number",
              //   model: "PHONE_NO",
              //   type: "vfg-the-mask",
              //   placeholder: "Enter telephone number",
              //   // mask: "######-##-####",
              //   mask: "0##-########",
              //   styleClasses: "col-md-6",
              //   required: true,
              //   validator: "string",
              // },
              // {
              //   labels: "Request TAC",
              //   model: "SMS_TAC_NUMBER",
              //   type: "vfg-custom-Tacinput",
              //   placeholder: "Enter TAC number",
              //   styleClasses: "col-md-6",
              //   required: true,
              //   // validator: "string",
              //   validator: ["string", this.checkTAC],

              // },
              // {
              //   type: "vueMultiSelect",
              //   model: "USER_SECURITY_QUESTION",
              //   label: "Choose Security Question",
              //   placeholder: "Select security question ",
              //   required: true,
              //   validator: "required",
              //   styleClasses: "col-md-12",
              //   selectOptions: {
              //     multiple: false,
              //     key: "SECURITY_QUESTION",
              //     label: "SECURITY_QUESTION",
              //     searchable: true,
              //   },
              //   values: (model, schema) => {
              //     return this.securityQuestion;
              //   },
              // },
              // {
              //   label: "Security Answer",
              //   type: "input",
              //   inputType: "text",
              //   model: "USER_SECURITY_ANSWER",
              //   placeholder: "Answer",
              //   required: true,
              //   validator: "string",
              //   styleClasses: "col-md-12",
              // },
            ],
          },
        ],
      },
      model: {
        USER_ID: localStorage.getItem("forgot-password-userid"),
        UID:localStorage.getItem("forgot-password-id"),
        USER_PASSWORD: "",
        CONFIRM_PASSWORD: "",
        // PHONE_NO: "",
        // SMS_TAC_NUMBER: "",
        USER_SECURITY_QUESTION: "",
        USER_SECURITY_ANSWER: "",
        //TAC_NUMBER:"",
      },
      formOptions: {
        // validationErrorClass: "has-error",
        // validationSuccessClass: "has-`success`",
        validateAfterChanged: true,
      },
    };
  },
  methods: {
    async getPasswordSetting() {
      const response = await services06Module0.getPasswordSetting(0);
      //console.log("get pass sett2=", response);
      this.passwordSetting = response;
    },
    validateUserID(value) {
       if (value === ""){
          return;
        }
    },
    validatePassword(value) {
      if (value === "") {
        return;
      }
      let idlength = value.length;
      if (idlength >= 1) {
        if (
          idlength < this.passwordSetting.MIN_LENGTH ||
          idlength > this.passwordSetting.MAX_LENGTH
        ) {
          if (idlength < this.passwordSetting.MIN_LENGTH) {
            return [
              `PASSWORD MIN LENGTH MUST BE ${this.passwordSetting.MIN_LENGTH}`,
            ];
          }
          if (idlength > this.passwordSetting.MAX_LENGTH) {
            return [
              `PASSWORD MAX LENGTH MUST BE ${this.passwordSetting.MAX_LENGTH}`,
            ];
          }
          return;
        }
        if (
          this.passwordSetting.UPPERCASE_LOWERCASE &&
          this.passwordSetting.SPECIAL_CHARACTERS &&
          this.passwordSetting.ALPHANUMERIC
        ) {
          let expression =
            /^(?=.*[0-9])(?=.*[a-z])(?=.*[A-Z])(?=.*?[#?!@$%^&*-])([a-zA-Z0-9#?!@$%^&*-]+)$/;
          if (!expression.test(value)) {
            return [
              "Password must contain at least one uppercase letter, one lower case letter, one number and one character",
            ];
          }
        } else if (
          this.passwordSetting.UPPERCASE_LOWERCASE &&
          this.passwordSetting.SPECIAL_CHARACTERS &&
          !this.passwordSetting.ALPHANUMERIC
        ) {
          let expression =
            /^(?=.*[a-z])(?=.*[A-Z])(?=.*?[#?!@$%^&*-])([a-zA-Z0-9#?!@$%^&*-]+)$/;
          if (!expression.test(value)) {
            return [
              "Password must contain at least one uppercase letter, one lower case letter, and one character",
            ];
          }
        } else if (
          this.passwordSetting.UPPERCASE_LOWERCASE &&
          !this.passwordSetting.SPECIAL_CHARACTERS &&
          !this.passwordSetting.ALPHANUMERIC
        ) {
          let expression = /^(?=.*[a-z])(?=.*[A-Z])([a-zA-Z0-9#?!@$%^&*-]+)$/;
          if (!expression.test(value)) {
            return [
              "Password must contain at least one uppercase letter and one lower case letter",
            ];
          }
        } else if (
          this.passwordSetting.UPPERCASE_LOWERCASE &&
          !this.passwordSetting.SPECIAL_CHARACTERS &&
          this.passwordSetting.ALPHANUMERIC
        ) {
          console.log("Test1");
          let expression =
            /^(?=.*[0-9])(?=.*[a-z])(?=.*[A-Z])([a-zA-Z0-9#?!@$%^&*-]+)$/;
          if (!expression.test(value)) {
            return [
              "Password must contain at least one number, one uppercase letter and one lower case letter",
            ];
          }
        } else if (
          !this.passwordSetting.UPPERCASE_LOWERCASE &&
          !this.passwordSetting.SPECIAL_CHARACTERS &&
          this.passwordSetting.ALPHANUMERIC
        ) {
          let expression =
            /^(?=.*[0-9])(?=.*[a-zA-Z])([a-zA-Z0-9#?!@$%^&*-]+)$/;
          if (!expression.test(value)) {
            return ["Password must contain at least one number and one letter"];
          }
        } else if (
          !this.passwordSetting.UPPERCASE_LOWERCASE &&
          this.passwordSetting.SPECIAL_CHARACTERS &&
          this.passwordSetting.ALPHANUMERIC
        ) {
          let expression =
            /^(?=.*[0-9])(?=.*[a-zA-Z])(?=.*?[#?!@$%^&*-])([a-zA-Z0-9#?!@$%^&*-]+)$/;
          if (!expression.test(value)) {
            return [
              "Password must contain at least one number, one letter and one special character",
            ];
          }
        } else if (
          !this.passwordSetting.UPPERCASE_LOWERCASE &&
          !this.passwordSetting.SPECIAL_CHARACTERS &&
          !this.passwordSetting.ALPHANUMERIC
        ) {
          let expression = /^([a-zA-Z0-9#?!@$%^&*-]+)$/;
          if (!expression.test(value)) {
            return [""];
          }
        } else if (
          !this.passwordSetting.UPPERCASE_LOWERCASE &&
          this.passwordSetting.SPECIAL_CHARACTERS &&
          !this.passwordSetting.ALPHANUMERIC
        ) {
          let expression = /^(?=.*?[#?!@$%^&*-])([a-zA-Z0-9#?!@$%^&*-]+)$/;
          if (!expression.test(value)) {
            return ["Password must contain at least one special character"];
          }
        }
      }
    },
    isEqualTo(value, field, model) {
      console.log(value);
      if (value != this.model.USER_PASSWORD) {
        return ["Password do not match"];
      }
    },
    checkTAC(value, field, model) {
      console.log(value);
      const tac_number=localStorage.getItem('TAC_NUMBER');
      if (value != tac_number) {
        return ["Wrong TAC number!"];
      }
    },
    async securityQuestionList() {
      const response = await services06Module1.securityQuestion();
      this.securityQuestion = response;
      //console.log("sec quest last" + JSON.stringify(this.securityQuestionList));
    },
    async changePass() {
      // if (this.$refs.recoverPasswordForm.validate()) {
        const data = new FormData();
        // alert("this is id : " + this.model.USER_ID);
        data.append("USER_ID", this.model.USER_ID);
        data.append("USER_PASSWORD", this.model.USER_PASSWORD);
       // data.append("USER_MOBILE_NUM", 60 + this.model.PHONE_NO);
       // data.append("SMS_TAC_NUMBER", this.model.SMS_TAC_NUMBER);
        data.append(
          "USER_SECURITY_QUESTION_ID",
          this.model.USER_SECURITY_QUESTION.SECURITY_ID);
        data.append("USER_SECURITY_ANSWER", this.model.USER_SECURITY_ANSWER);
        data.append("USER_ROLES", "distributor_admin");
        //data.append("_method", "PUT");
         const response = await services06Module1.changeDistributorPassword(data);
         if(response.data == 4707){
            alert(
            "This Password is Exist in your History. Please Choose New One."
            );
            this.model.USER_PASSWORD == "";
            this.model.CONFIRM_PASSWORD == "";
            this.model.USER_SECURITY_QUESTION.SECURITY_ID = "";
            this.model.USER_SECURITY_ANSWER = "";
             //this.$router.push({ name: "last-password-distributor" });
         }else{
        alert(
          "Password reset is success, Please log in using the new password"
        );

        //clear  localStorage value
        localStorage.removeItem('TAC_NUMBER');
        localStorage.removeItem('forgot-password-id');
        localStorage.removeItem('forgot-password-userid');
        localStorage.removeItem('forgot-password-mobile');
        localStorage.removeItem('forgot-password-email');
        this.$router.push({ name: "login-distributor" });
         }
       // this.$router.go(-1);
      // }

    },
  },
};
</script>

<style lang="scss">
.text-color {
  color: white;
}
</style>
