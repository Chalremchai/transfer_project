<template>
  <div class="pt-4">
    <v-data-table
      :headers="headers"
      :items="profileAll"
      :search="search"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>รายชื่อผู้ใช้</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>

          <v-spacer></v-spacer>
          <div v-if="userRole == 'admin'">
            <v-dialog v-model="dialog" max-width="90%">
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  color=""
                  elevation="0"
                  fab
                  small
                  class="mb-2"
                  v-bind="attrs"
                  v-on="on"
                >
                  <v-icon> mdi-plus</v-icon>
                </v-btn>
              </template>
              <v-card>
                <v-card-title>
                  <span class="text-h5">{{ formTitle }}</span>
                </v-card-title>

                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="4" md="4">
                        <v-select
                          v-model="editedItem.title"
                          :items="titleUsers"
                          label="คำนำหน้า"
                          outlined
                          dense
                          item-value="name"
                          item-text="name"
                        ></v-select>
                      </v-col>
                      <v-col cols="12" sm="4" md="4">
                        <v-text-field
                          v-model="editedItem.first_name"
                          label="ชื่อ"
                          outlined
                          dense
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="4" md="4">
                        <v-text-field
                          v-model="editedItem.last_name"
                          label="นามสกุล"
                          outlined
                          dense
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="editedItem.username"
                          label="Username"
                          outlined
                          dense
                        ></v-text-field>
                      </v-col>

                      <v-col
                        sm="6"
                        cols="12"
                        md="4"
                        v-if="formTitle == 'เพิ่มข้อมูลผู้ใช้'"
                      >
                        <v-text-field
                          v-model="editedItem.password"
                          :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'"
                          :rules="[rules.required]"
                          :type="show1 ? 'text' : 'password'"
                          name="input-10-1"
                          label="รหัสผ่าน"
                          counter
                          outlined
                          dense
                          @click:append="show1 = !show1"
                        ></v-text-field>
                      </v-col>
                      <v-col
                        sm="6"
                        cols="12"
                        md="4"
                        v-if="formTitle == 'เพิ่มข้อมูลผู้ใช้'"
                      >
                        <v-text-field
                          v-model="editedItem.confirm_password"
                          :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'"
                          :rules="[rules.required]"
                          :type="show1 ? 'text' : 'password'"
                          name="input-10-1"
                          label="ยืนยันรหัสผ่าน"
                          counter
                          outlined
                          dense
                          @click:append="show1 = !show1"
                        ></v-text-field>
                      </v-col>

                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="editedItem.email"
                          :rules="[rules.required, rules.email]"
                          label="E-mail"
                          required
                          outlined
                          dense
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-select
                          v-model="editedItem.role.role"
                          label="Role"
                          :items="role"
                          item-text="name"
                          item-value="id"
                          outlined
                          dense
                        ></v-select>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="error" text @click="close"> ยกเลิก </v-btn>
                  <v-btn color="blue darken-1" text @click="save">
                    บันทึก
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </div>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5"
                >ยืนยันต้องการลบรายวิชานี้ออกจากรายวิชาของมหาวิทยาลัย?</v-card-title
              >
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="error" text @click="closeDelete">ยกเลิก</v-btn>
                <v-btn color="blue darken-1" text @click="deleteItemConfirm"
                  >ตกลง</v-btn
                >
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
        <v-col class="pb-0">
          <v-text-field
            v-model="search"
            label="ค้นหา"
            outlined
            dense
            class=""
          ></v-text-field>
        </v-col>
      </template>
      <template v-slot:[`item.actions`]="{ item }">
        <div v-if="userRole == 'admin'">
          <v-icon small color="primary" class="mr-2" @click="editItem(item)">
            mdi-pencil
          </v-icon>
          <v-icon small color="red" @click="deleteItem(item)">
            mdi-delete
          </v-icon>
        </div>
        <div v-else>
          <v-icon
            small
            color="primary"
            disabled
            class="mr-2"
            @click="editItem(item)"
          >
            mdi-pencil
          </v-icon>
          <v-icon small color="red" disabled @click="deleteItem(item)">
            mdi-delete
          </v-icon>
        </div>
      </template>
    </v-data-table>
  </div>
</template>
<script>
import { mapActions, mapState } from "vuex";

export default {
  data: () => ({
    dialog: false,
    search: "",
    dialogDelete: false,
    image: null,
    base64: "",
    show1: false,
    idSubject: "",
    rules: {
      required: (value) => !!value || "จำเป็นต้องระบุ",
      counter: (value) => value.length <= 20 || "Max 20 characters",
      email: (value) => {
        const pattern =
          /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
        return pattern.test(value) || "Invalid e-mail.";
      },
    },
    role: [
      {
        id: "admin",
        name: "admin",
      },
      {
        id: "teacher",
        name: "teacher",
      },
      {
        id: "student",
        name: "student",
      },
    ],
    headers: [
      {
        text: "คำนำหน้า",
        align: "start",
        sortable: false,
        value: "title",
      },

      {
        text: "ชื่อ",
        align: "start",
        sortable: false,
        value: "first_name",
      },
      { text: "นามสกุล", value: "last_name", sortable: false },
      { text: "username", value: "username", sortable: false },
      { text: "role", value: "role.role", sortable: false },
      { text: "email", value: "email", sortable: false },
      { text: "ตัวดำเนินการ", value: "actions", sortable: false },
    ],
    desserts: [],
    editedIndex: -1,
    editedItem: {
      title: "",
      first_name: "",
      last_name: "",
      username: "",
      password: "",
      confirm_password: "",
      role: {
        role: "",
      },
      email: "",
    },
    defaultItem: {
      title: "",
      first_name: "",
      last_name: "",
      username: "",
      password: "",
      confirm_password: "",
      role: {
        role: "",
      },
      email: "",
    },
  }),

  computed: {
    titleUsers: {
      get() {
        if (this.$store.state.users.title) {
          return this.$store.state.users.title.results;
        } else {
          return null;
        }
      },
      set() {},
    },
    formTitle() {
      return this.editedIndex === -1
        ? "เพิ่มข้อมูลผู้ใช้"
        : "แก้ไขช้อมูลผู้ใช้";
    },
    schoolCourse: {
      get() {
        if (this.$store.state.subject.schoolCourse) {
          return this.$store.state.subject.schoolCourse.results;
        } else {
          return null;
        }
      },
      set() {},
    },
    myCourse: {
      get() {
        if (this.$store.state.subject.myCourse) {
          return this.$store.state.subject.myCourse.results;
        } else {
          return null;
        }
      },
      set() {},
    },
    userId: {
      get() {
        if (this.$store.state.users.userId) {
          return this.$store.state.users.userId;
        } else {
          return null;
        }
      },
      set() {},
    },
    userRole: {
      get() {
        if (this.$store.state.users.userLogin.user) {
          return this.$store.state.users.userLogin.user.role.role;
        } else {
          return null;
        }
      },
      set() {},
    },
    profileAll: {
      get() {
        if (this.$store.state.users.profileAll) {
          return this.$store.state.users.profileAll.results;
        } else {
          return null;
        }
      },
      set() {},
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },

  created() {},
  mounted() {
    this.getSchoolCourse();
    this.getMyCourse();
    this.getTitleUsers();
    this.getProfileAll();
  },
  methods: {
    ...mapActions({
      getSchoolCourse: "subject/getSchoolCourse",
      getMyCourse: "subject/getMyCourse",
      getTitleUsers: "users/getTitle",
      getProfileAll: "users/getProfileAll",
    }),
    download(item) {
      console.log("item", item);
      const linkSource = item.description_file;
      const downloadLink = document.createElement("a");
      const fileName = "คำอธิบายรายวิชา.pdf";

      downloadLink.href = linkSource;
      downloadLink.download = fileName;
      downloadLink.click();
    },

    editItem(item) {
      this.editedIndex = this.profileAll.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
      this.idSubject = item.id;
      console.log("item", this.idSubject, item);
    },

    deleteItem(item) {
      this.editedIndex = this.profileAll.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
      this.idSubject = item.id;
      console.log("item", this.idSubject, item);
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    deleteItemConfirm() {
      let id = this.idSubject;
      this.$fixedKeyApi.delete(`/users/${id}`).then((response) => {
        console.log("delete", response);
        if (response.status == 204) {
          this.closeDelete();
          this.getProfileAll();
        }
      });
    },
    save() {
      if (this.editedIndex > -1) {
        let id = this.idSubject;
        let dataProfile = {
          title: this.editedItem.title,
        };
        let data = {
          title: this.editedItem.title,
          first_name: this.editedItem.first_name,
          last_name: this.editedItem.last_name,
          username: this.editedItem.username,
          password: this.editedItem.password,
          confirm_password: this.editedItem.confirm_password,
          email: this.editedItem.email,
          role: this.editedItem.role.role,
        };
        this.$fixedKeyApi.patch(`/users/${id}/`, data).then((response) => {
          // if (response.status == 200) {
          console.log("patch", response.data);
          // this.close();
          // this.getProfileAll();
          this.$fixedKeyApi
            .patch(`/profile/${id}/`, dataProfile)
            .then((response) => {
              if (response.status == 200) {
                console.log("patch", response.data);
                this.close();
                this.getProfileAll();
              }
            });
          // }
        });
      } else {
        let data = {
          title: this.editedItem.title,
          first_name: this.editedItem.first_name,
          last_name: this.editedItem.last_name,
          username: this.editedItem.username,
          password: this.editedItem.password,
          confirm_password: this.editedItem.confirm_password,
          email: this.editedItem.email,
          role: this.editedItem.role.role,
        };
        this.$fixedKeyApi.post(`/users/`, data).then((response) => {
          if (response.data) {
            console.log("post", response.data);
            this.close();
            this.getProfileAll();
          }
        });
      }
    },
  },
};
</script>
