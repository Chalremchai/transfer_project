<template>
  <div>
    <v-card class="ma-2 pa-3" elevation="0">
      <v-row>
        <v-col cols="4" class="pl-2 pr-2 pt-2 pb-0">
          <v-combobox
            label="รายวิชาที่ขอเทียบที่ 1"
            :items="MyCourseCheck"
            item-text="course_title"
            item-value="id"
            outlined
            dense
            @v-on:change="onChange()"
            v-model="nameSubjectTransfer"
          ></v-combobox>
        </v-col>
        <v-col cols="2" class="pl-2 pr-2 pt-2 pb-0">
          <v-text-field
            label="เกรดวิชาที่ 1"
            outlined
            dense
            @v-on:change="onChange()"
            v-model="gradeOne"
          ></v-text-field>
        </v-col>
        <v-col cols="4" class="pl-2 pr-2 pt-2 pb-0">
          <v-combobox
            label="รายวิชาที่ขอเทียบที่ 2"
            :items="MyCourseCheck"
            item-text="course_title"
            item-value="id"
            outlined
            dense
            @v-on:change="onChange()"
            v-model="nameSubjectTransfer2"
          ></v-combobox>
        </v-col>
        <v-col cols="2" class="pl-2 pr-2 pt-2 pb-0">
          <v-text-field
            label="เกรดวิชาที่ 1"
            outlined
            dense
            @v-on:change="onChange()"
            v-model="gradeTwo"
          ></v-text-field>
        </v-col>
        <v-col cols="4" class="pl-2 pr-2 pt-2 pb-0">
          <v-combobox
            label="รายวิชาที่ขอเทียบที่ 3"
            :items="MyCourseCheck"
            item-text="course_title"
            item-value="id"
            outlined
            dense
            @v-on:change="onChange()"
            v-model="nameSubjectTransfer3"
          ></v-combobox>
        </v-col>
        <v-col cols="2" class="pl-2 pr-2 pt-2 pb-0">
          <v-text-field
            label="เกรดวิชาที่ 1"
            outlined
            dense
            @v-on:change="onChange()"
            v-model="gradeThere"
          ></v-text-field>
        </v-col>
        <v-col
          cols="6"
          class="pl-2 pr-2 pt-2 pb-0"
          v-if="nameSubjectTransfer != null"
        >
          <v-combobox
            label="รายวิชาที่ต้องการเทียบ"
            outlined
            :items="schoolCourse"
            item-text="course_title"
            item-value="id"
            dense
            v-on:change="onChange()"
            v-model="nameSubject"
          ></v-combobox>
        </v-col>
        <v-col cols="6" class="pl-2 pr-2 pt-2 pb-0" v-else>
          <v-combobox
            label="รายวิชาที่ต้องการเทียบ"
            outlined
            :items="schoolCourse"
            item-text="course_title"
            item-value="id"
            dense
            disabled
            v-on:change="onChange()"
            v-model="nameSubject"
          ></v-combobox>
        </v-col>
        <v-col cols="12" class="pl-1 pt-0">
          <v-btn
            color="red"
            dark
            class="float-right"
            rounded
            elevation="0"
            small
            @click="removeFormElement"
            ><v-icon small left>mdi-delete</v-icon>ลบ</v-btn
          >
        </v-col>
      </v-row>
    </v-card>
  </div>
</template>
<script>
import { mapActions, mapState } from "vuex";

export default {
  props: ["index", "lengths", "_uuid"],
  data() {
    return {
      creditTransfer: null,
      subjectDetailTransfer: null,
      nameSubjectTransfer: null,
      nameSubjectTransfer2: null,
      nameSubjectTransfer3: null,
      gradeOne: 0,
      gradeTwo: 0,
      gradeThere: 0,
      subjectSerailTransfer: null,
      credit: null,
      subjectDetail: null,
      nameSubject: null,
      nameCourse: null,
      subjectSerail: null,
    };
  },
  computed: {
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
    MyCourseCheck: {
      get() {
        if (this.$store.state.subject.MyCourseCheck) {
          return this.$store.state.subject.MyCourseCheck.results;
        } else {
          return null;
        }
      },
      set() {},
    },
    equivalentCourse: {
      get() {
        if (this.$store.state.transfer.equivalentCourse) {
          return this.$store.state.transfer.equivalentCourse.results;
        } else {
          return null;
        }
      },
      set() {},
    },
  },
  mounted() {
    this.getSchoolCourse();
    this.getMyCourse();
    this.getEquivalentCourse();
    this.getMyCourseCheck();
  },
  methods: {
    ...mapActions({
      getSchoolCourse: "subject/getSchoolCourse",
      getMyCourseCheck: "subject/getMyCourseCheck",
      getMyCourse: "subject/getMyCourse",

      getEquivalentCourse: "transfer/getEquivalentCourse",
    }),
    removeFormElement() {
      console.log("delete", this.lengths, this.index);
      let lengths = this.lengths;
      lengths.splice(this.index, 1);
      this.lengths = lengths;
      this.$emit("onChange");
    },
    async onChange() {
      await this.$emit("onChange");
      await this.$emit("update", {
        index: this.index,
        nameSubjectTransfer: this.nameSubjectTransfer,
        nameSubjectTransfer2: this.nameSubjectTransfer2,
        nameSubjectTransfer3: this.nameSubjectTransfer3,
        gradeOne: this.gradeOne,
        gradeTwo: this.gradeTwo,
        gradeThere: this.gradeThere,
        nameSubject: this.nameSubject,
        nameCourse: this.nameCourse,
      });
    },
  },
};
</script>
