<template>
  <div class="pt-4">
    <v-data-table
      :headers="headers"
      :items="myCourse"
      :search="search"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>คลังรายวิชาที่ขอเทียบ</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>

          <v-spacer></v-spacer>
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
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.course_code"
                        label="รหัสวิชา"
                        outlined
                        dense
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.course_title"
                        label="ชื่อวิชา"
                        outlined
                        dense
                      ></v-text-field>
                    </v-col>

                    <v-col cols="12" sm="6" md="4">
                      <v-select
                        v-model="editedItem.credit_type"
                        label="ประเภทรายวิชา"
                        :items="type"
                        item-text="name"
                        item-value="id"
                        outlined
                        dense
                      ></v-select>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.credit"
                        label="หน่วยกิจ"
                        outlined
                        dense
                      ></v-text-field>
                    </v-col>
                    <!-- <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="editedItem.grade"
                          label="เกรด"
                          outlined
                          dense
                        ></v-text-field>
                      </v-col> -->
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.school"
                        label="วิทยาลัย/มหาวิทยาลัย"
                        outlined
                        dense
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.course_year"
                        label="ปี พ.ศ"
                        outlined
                        dense
                      ></v-text-field>
                    </v-col>
                    <v-col
                      cols="12"
                      sm="6"
                      md="4"
                      v-if="formTitle == 'แก้ไขรายวิชาที่ขอเทียบ'"
                    >
                      <v-text-field
                        v-model="editedItem.status"
                        label="สถานะ"
                        outlined
                        dense
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12" md="12">
                      <!-- <v-text-field
                        v-model="editedItem.description_file"
                        label="คำอธิบายรายวิชา"
                        outlined
                        dense
                      ></v-text-field> -->
                      <!-- <v-file-input
                        outlined
                        dense
                        label="คำอธิบายรายวิชา"
                        v-model="image"
                        @change="uploadImage(image)"
                      ></v-file-input> -->
                      <v-textarea
                        outlined
                        dense
                        label="คำอธิบายรายวิชา"
                        v-model="editedItem.description_file"
                      ></v-textarea>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="error" text @click="close"> ยกเลิก </v-btn>
                <v-btn color="blue darken-1" text @click="save"> บันทึก </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
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
const dataURItoBlob = (dataURI) => {
  const bytes =
    dataURI.split(",")[0].indexOf("base64") >= 0
      ? atob(dataURI.split(",")[1])
      : unescape(dataURI.split(",")[1]);
  const mime = dataURI.split(",")[0].split(":")[1].split(";")[0];
  const max = bytes.length;
  const ia = new Uint8Array(max);
  for (let i = 0; i < max; i += 1) ia[i] = bytes.charCodeAt(i);
  return new Blob([ia], { type: mime });
};

const resizeImage = ({ file, maxSize }) => {
  const reader = new FileReader();
  const image = new Image();
  const canvas = document.createElement("canvas");

  const resize = () => {
    let { width, height } = image;

    if (width > height) {
      if (width > maxSize) {
        height *= maxSize / width;
        width = maxSize;
      }
    } else if (height > maxSize) {
      width *= maxSize / height;
      height = maxSize;
    }

    canvas.width = width;
    canvas.height = height;
    canvas.getContext("2d").drawImage(image, 0, 0, width, height);

    const dataUrl = canvas.toDataURL("image/jpeg");

    return dataURItoBlob(dataUrl);
  };

  return new Promise((ok, no) => {
    reader.onload = (readerEvent) => {
      image.onload = () => ok(resize());
      image.src = readerEvent.target.result;
    };

    reader.readAsDataURL(file);
  });
};
export default {
  data: () => ({
    dialog: false,
    search: "",
    dialogDelete: false,
    image: null,
    base64: "",
    idSubject: "",

    type: [
      {
        id: "ท",
        name: "ทฤษฎี",
      },
      {
        id: "ป",
        name: "ปฏิบัติ",
      },
    ],
    headers: [
      {
        text: "รหัสวิชา",
        align: "start",
        sortable: false,
        value: "course_code",
      },
      { text: "ชื่อวิชา", value: "course_title", sortable: false },
      { text: "หน่วยกิจ", value: "credit", sortable: false },

      { text: "ประเภทรายวิชา", value: "credit_type", sortable: false },
      { text: "สถานะ", value: "status", sortable: false },
      { text: "ตัวดำเนินการ", value: "actions", sortable: false },
    ],
    desserts: [],
    editedIndex: -1,
    editedItem: {
      id: 0,
      course_code: "",
      course_title: "",
      course: 0,
      credit_type: 0,
      credit: 0,
      description_file: "",
      grade: "",
      school: "",
      status: "",
    },
    defaultItem: {
      id: 0,
      course_code: "",
      course_title: "",
      course: 0,
      credit_type: 0,
      credit: 0,
      description_file: "",
      grade: "",
      school: "",
      status: "",
    },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1
        ? "เพิ่มรายวิชาที่ขอเทียบ"
        : "แก้ไขรายวิชาที่ขอเทียบ";
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
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },

  mounted() {
    this.getSchoolCourse();
    this.getMyCourse();
  },
  methods: {
    ...mapActions({
      getSchoolCourse: "subject/getSchoolCourse",
      getMyCourse: "subject/getMyCourse",
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
    async uploadImage(image) {
      console.log("image", image);
      if (image) {
        let file = await image;

        const reader = new FileReader();
        reader.readAsDataURL(file);

        reader.onload = function () {
          console.log(reader.result);
        };
        reader.onerror = function (error) {
          console.log("Error: ", error);
        };
        this.base64 = reader;
        console.log("base64", this.base64);
      }
    },

    editItem(item) {
      this.editedIndex = this.myCourse.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
      this.idSubject = item.id;
      console.log("item", this.idSubject, item);
    },

    deleteItem(item) {
      this.editedIndex = this.myCourse.indexOf(item);
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

      this.$fixedKeyApi.delete(`/my-course/${id}`).then((response) => {
        console.log("delete", response);
        if (response.status == 204) {
          this.closeDelete();
          this.getMyCourse();
        }
      });
    },
    save() {
      if (this.editedIndex > -1) {
        let id = this.idSubject;
        let data = {
          course_code: this.editedItem.course_code,
          course_title: this.editedItem.course_title,
          credit_type: this.editedItem.credit_type,
          credit: this.editedItem.credit,
          grade: this.editedItem.grade,
          school: this.editedItem.school,
          course: null,
          subject: null,
          status: this.editedItem.status,
          course_year: this.editedItem.course_year,
          description_file: this.editedItem.description_file,
          created_user: this.userId,
        };
        this.$fixedKeyApi.patch(`/my-course/${id}/`, data).then((response) => {
          if (response.status == 200) {
            console.log("patch", response.data);
            this.close();
            this.getMyCourse();
          }
        });
      } else {
        let data = {
          course_code: this.editedItem.course_code,
          course_title: this.editedItem.course_title,
          credit_type: this.editedItem.credit_type,
          credit: this.editedItem.credit,
          grade: this.editedItem.grade,
          school: this.editedItem.school,
          course: null,
          subject: null,
          status: "ยังไม่ตรวจสอบ",
          course_year: this.editedItem.course_year,
          description_file: this.editedItem.description_file,
          created_user: this.userId,
        };
        this.$fixedKeyApi.post(`/my-course/`, data).then((response) => {
          if (response.data) {
            console.log("post", response.data);
            this.close();
            this.getMyCourse();
          }
        });
      }
    },
  },
};
</script>
