<template>
  <div>
    <v-content>
      <v-container fluid fill-height>
        <v-layout align-center justify-center>
          <v-flex s12 sm8 md4>
            <v-card class="elevation-12">
              <v-toolbar color="blue-grey" dark flat>
                <v-toolbar-title>Account details</v-toolbar-title>
                <v-spacer></v-spacer>
              </v-toolbar>

              <v-card-text>
                <v-form :lazy-validation="lazy" ref="form">
                  <v-text-field
                    label="First Name"
                    name="name"
                    v-model="firstName"
                    prepend-icon="mdi-account"
                    type="text"
                    :rules="[rules.required]"
                  ></v-text-field>

                  <v-text-field
                    label="Last Name"
                    name="lastname"
                    v-model="lastName"
                    prepend-icon="mdi-account"
                    type="text"
                    :rules="[rules.required]"
                  ></v-text-field>

                  <v-text-field
                    label="Email"
                    name="email"
                    v-model="email"
                    prepend-icon="mdi-email"
                    type="text"
                    :rules="[rules.required]"
                  ></v-text-field>

                  <v-text-field
                    id="password"
                    label="Enter the new password"
                    name="password"
                    v-model="password1"
                    prepend-icon="mdi-lock"
                    type="password"
                  ></v-text-field>

                  <v-text-field
                    id="password2"
                    label="Repeat the new password"
                    name="password"
                    v-model="password2"
                    prepend-icon="mdi-lock"
                    type="password"
                  ></v-text-field>

                </v-form>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn @click="validate" color="blue-grey white--text">Save</v-btn>
              </v-card-actions>
            </v-card>


          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
  </div>
</template>

<script>
import { mapActions, mapGetters } from "vuex";
export default {
  components: {},

  data() {
    return {
      firstName: "",
      lastName: "",
      password1: "",
      password2: "",
      email :"",
      file : null,
      rules: {
        required: value => !!value || "Required."
      },
    };
  },

  computed: {
    ...mapGetters({
      getUser: "users/getUser"
    })
  },

  created() {
    this.firstName = this.getUser.firstName;
    this.lastName = this.getUser.lastName;
    this.email = this.getUser.email;
  },

  methods: {
    ...mapActions({
      changeUserAction: "users/updateAccount",
      showSnackbar: "snackbar/showSnackbar"
    }),

    validate() {
      if (this.$refs.form.validate()) {
        this.submit();
      } else {
        this.showSnackbar(["You need to fill out all fields!", "error", "bottom"]);
      }
    },

    fileSubmited(file) {
      this.file = file;
      this.editedItem.logo = file.name;
    },

    submit() {
      let userInformationChanged = false;
      let passwordChanged = false;
      if (this.getUser.firstName != this.firstName) {
        userInformationChanged = true;
      }

      if(this.getUser.email != this.email){
        userInformationChanged = true;
      }

      if (this.getUser.lastName != this.lastName) {
        userInformationChanged = true;
      }

      if (this.password1.trim() != "") {
        userInformationChanged = true;
        passwordChanged = true;

        if (this.password1 != this.password2) {
          this.showSnackbar(["Passwords don't match!", "error", "bottom"]);
          return;
        }
      }

      if (!userInformationChanged) {
        this.showSnackbar(["No fields were changed!", "error", "bottom"]);
        return;
      }

      let user = {
        firstName: this.firstName,
        lastName: this.lastName,
        email: this.email,
        role: this.getUser.role,
        organizationName: this.getUser.organizationName,
        id : this.getUser.id,
        likesDark : this.getUser.likesDark
      };

      if (passwordChanged) {
        user.password = this.password1;
      }

      this.changeUserAction(user)
        .then(() => {
          this.showSnackbar(["You have successfully edited your account!", "success", "bottom"]);
          this.password1 = "";
          this.password2 = "";
        })
        .catch(error =>  this.showSnackbar([error.response.data, "error", "bottom"]))
    }
  }
};
</script>

