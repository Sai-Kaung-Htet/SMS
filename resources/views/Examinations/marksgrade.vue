<template>
  <div class="form" id="bar">
    <div class="toplink">
      <h2 class="stuName">Examinations</h2>
      <h4 class="stuLink">
        <router-link to="/dashboard" class="home">Home</router-link>> Marks Grade
      </h4>
    </div>
    <hr />
    <Loading></Loading>
    <confirm :url="props"></confirm>
    <div class="row rowContainer" style="align-items: end !important;margin:0;">
      <div class="col-lg-5 col-md-12" style="padding:0;">
        <div class="card">
          <div class="card-header">
            <h6>Add Grade</h6>
          </div>
          <div class="card-body" style="padding:1rem 0;border-bottom: 1px solid #8080808c;">
            <message :alertmessage="msg" id="alertmsg" />
            <div class="col-12">
              <label for="gradename">
                Grade Name
                <strong>*</strong>
              </label>
              <input
                id="nameid"
                type="text"
                class="inputbox"
                name="gradename"
                v-model="saveMarksGrade.name"
                @keyup="onValidate(saveMarksGrade.name, 'nameid', 'namemsg')"
                v-on:blur="onValidate(saveMarksGrade.name, 'nameid', 'namemsg')"
              />
              <span id="namemsg" class="error_message">Grade Name is required</span>
            </div>
            <div class="col-12">
              <label for="percent">
                Percent Form
                <strong>*</strong>
              </label>
              <input
                id="markFrom"
                type="text"
                class="inputbox"
                name="percent"
                v-model="saveMarksGrade.mark_from"
                @keyup="onValidate(saveMarksGrade.mark_from, 'markFrom', 'markFrommsg')"
                v-on:blur="onValidate(saveMarksGrade.mark_from, 'markFrom', 'markFrommsg')"
              />
              <span id="markFrommsg" class="error_message">Marks_from field is required</span>
            </div>
            <div class="col-12">
              <label for="upto">
                Percent Upto
                <strong>*</strong>
              </label>
              <input
                id="marksUpto"
                type="text"
                class="inputbox"
                name="upto"
                v-model="saveMarksGrade.marks_upto"
                @keyup="onValidate(saveMarksGrade.marks_upto, 'marksUpto', 'marksUptomsg')"
                v-on:blur="onValidate(saveMarksGrade.marks_upto, 'marksUpto', 'marksUptomsg')"
              />
              <span id="marksUptomsg" class="error_message">Marks_upto field is required</span>
            </div>
            <div class="col-12 end">
              <label for="description">Description</label>
              <textarea class="textareas" rows="3" v-model="saveMarksGrade.description"></textarea>
            </div>
            <div class="col-12 column-12">
              <button class="save" id="globalSave" @click="SaveMarksGrade()">Save</button>
            </div>
          </div>
        </div>
      </div>

      <div class="col-lg-7 col-md-12" style="padding:0;">
        <div class="card">
          <div class="card-header">
            <h6>Grade List</h6>
          </div>
          <div class="card-body">
            <message :alertmessage="deletemsg" id="delalertmsg" />
            <input
              v-on:keyup="searchTable()"
              type="text"
              placeholder="Search..."
              class="searchText"
              id="myInput"
            />
            <div class="copyRows">
              <div class="row" id="copyRow">
                <div class="col-3">
                  <a href="#" title="Excel" @click.prevent="downloadExcel('studenttable', 'name', 'MarksGrade.xls')">
                    <i class="fa fa-file-excel-o"></i>
                  </a>
                </div>
                <div class="col-3">
                  <a href="#" title="Print" @click.prevent="printme('print')">
                    <i class="fa fa-print"></i>
                  </a>
                </div>
                <div class="col-3">
                  <a onclick="showColumns()" title="Columns">
                    <i class="fa fa-columns"></i>
                  </a>
                  <div id="columns" class="columns">
                    <p onclick="showTableHeader('Grade Name')" id="Grade Name" class="tableLink">
                      <span>Grade Name</span>
                    </p>
                    <p
                      onclick="showTableHeader('Percent Form')"
                      id="Percent Form"
                      class="tableLink"
                    >
                      <span>Percent Form</span>
                    </p>
                    <p
                      onclick="showTableHeader('Percent Upto')"
                      id="Percent Upto"
                      class="tableLink"
                    >
                      <span>Percent Upto</span>
                    </p>
                    <p onclick="showTableHeader('Action')" id="Action" class="tableLink">
                      <span>Action</span>
                    </p>
                    <p
                      class="tableLinks"
                      @click="allTableHeader('Grade Name','Percent Form','Percent Upto','Action')"
                      style="border-radius: 0 0 5px 5px;"
                    >
                      <span>Restore visibility</span>
                    </p>
                  </div>
                </div>
              </div>
            </div>
            <div v-if="data == false">
              <h1 class="NoData">No Data</h1>
            </div>
            <div v-else class="table-responsive" id="print">
              <table class="table table-hover table-striped" id="studenttable">
                <thead>
                  <tr>
                    <th class="all" nowrap>Grade Name</th>
                    <th class="all" nowrap>Percent Form</th>
                    <th class="all" nowrap>Percent Upto</th>
                    <th class="all" nowrap>Action</th>
                  </tr>
                </thead>
                <tbody id="myTable">
                  <tr class="active" v-for="MarksGrade in marksGrade" :key="MarksGrade.id">
                    <td class="all" nowrap>
                      <p class="toolText">
                        {{MarksGrade.name}}
                        <span class="tooltipLabel">{{MarksGrade.description}}</span>
                      </p>
                    </td>
                    <td class="all" nowrap>{{MarksGrade.mark_from}}%</td>
                    <td class="all" nowrap>{{MarksGrade.marks_upto}}%</td>
                    <td>
                      <i class="fa fa-pencil pen" @click="EditMarksGrade(MarksGrade.id)">
                        <span class="penLabel">Edit</span>
                      </i>
                      <i data-toggle="modal"
                        data-target="#exampleModalCenter" class="fa fa-trash time" @click="DeleteMarksGrade(MarksGrade.id)">
                        <span class="timeLabel">Delete</span>
                      </i>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- <button type="button" @click="Test()">Test</button> -->
  </div>
</template>
<script>
import { EventBus } from "../../js/event-bus.js";
import Loading from "../LoadingController.vue";
import confirm from "../message/confirm.vue";
import message from '../Alertmessage/message.vue';
import { Util } from "../../js/util";
export default {
      components: {
          Loading,
          confirm,
          message
        },
  data() {
    return {
      props: {
                    url: "",
                    type: ""
                },
                msg: {
                text: "",
                type: ""
                },
                deletemsg: {
                text: "",
                type: ""        
                },
      marksGrade: [],
      saveMarksGrade: {},
      successAlertmsg: "",
      successAlertmsg1: "",
      erroralertmsg: "",
      savebutton: "",
      errors: [],
      check: false,
      error: {},
      arrayError: [],
      data : false
    };
  },
  mounted(){
    EventBus.$emit('onLoad');
  },
  created() {
    EventBus.$emit("ThemeClicked");
    this.getmarksGrades();
    EventBus.$on("clicked", response => {
            this.getmarksGrades();
            this.deletemsg.text = 'Deleted Successfully',
            this.deletemsg.type = 'success'
            Util.workAlert("#delalertmsg");
            });
  },
  methods: {
    allTableHeader(id, id1, id2, id3) {
      document.getElementById(id).style.background = "#1b5e20";
      document.getElementById(id).style.color = "white";
      document.getElementById(id1).style.background = "#1b5e20";
      document.getElementById(id1).style.color = "white";
      document.getElementById(id2).style.background = "#1b5e20";
      document.getElementById(id2).style.color = "white";
      document.getElementById(id3).style.background = "#1b5e20";
      document.getElementById(id3).style.color = "white";
    },
    getmarksGrades() {
      this.axios.get(`/api/marksGrade/getMarksGrade`).then(response => {
        this.marksGrade = response.data;
        if(this.marksGrade.length > 0){
                      this.data = true;
                    }else{
                      this.data = false;
                    }
                    EventBus.$emit('onLoadEnd');
      });
    },
    SaveMarksGrade() {
      this.checkValidate();
      if (this.check == true) {
        EventBus.$emit('onLoad');
        this.successAlertmsg = "exam Added Successfully";
        if (
          this.saveMarksGrade.description == undefined ||
          this.saveMarksGrade.description == ""
        ) {
          this.saveMarksGrade.description = "No description";
        }
        this.axios
          .post(`/api/marksGrade/addMarksGrade`, this.saveMarksGrade)
          .then(response => {
            this.saveMarksGrade = {};
            this.getmarksGrades();
            this.msg.text = response.data,
                this.msg.type = 'success',
                Util.workAlert("#alertmsg")
          });
      } else {
        console.log("required");
      }
    },
    searchTable() {
      var input, filter, found, table, tr, td, i, j;
      input = document.getElementById("myInput");
      filter = input.value.toUpperCase();
      table = document.getElementById("myTable");
      tr = table.getElementsByTagName("tr");
      for (i = 0; i < tr.length; i++) {
        td = tr[i].getElementsByTagName("td");
        for (j = 0; j < td.length; j++) {
          if (td[j].innerHTML.toUpperCase().indexOf(filter) > -1) {
            found = true;
          }
        }
        if (found) {
          tr[i].style.display = "";
          found = false;
        } else {
          tr[i].style.display = "none";
        }
      }
    },
    onValidate(value, inputId, megId) {
      if (value == "" || value == undefined)
        document.getElementById(inputId).style.border = "solid 1px red";
      else {
        document.getElementById(inputId).style.border = "solid 1px #d2d6de";
        document.getElementById(megId).style.display = "none";
      }
    },

    onValidateMessage(inputId, megId) {
      document.getElementById(inputId).style.border = "solid 1px red";
      document.getElementById(megId).style.display = "block";
    },

    checkValidate() {
      if (
        this.saveMarksGrade.name == "" ||
        this.saveMarksGrade.name == undefined
      ) {
        this.onValidateMessage("nameid", "namemsg");
        this.arrayError.push("noop");
      }
      if (
        this.saveMarksGrade.mark_from == "" ||
        this.saveMarksGrade.mark_from == undefined
      ) {
        this.onValidateMessage("markFrom", "markFrommsg");
        this.arrayError.push("noop");
      }
      if (
        this.saveMarksGrade.marks_upto == "" ||
        this.saveMarksGrade.marks_upto == undefined
      ) {
        this.onValidateMessage("marksUpto", "marksUptomsg");
        this.arrayError.push("noop");
      }
      if (this.arrayError.length > 0) {
        this.check = false;
      } else {
        this.check = true;
      }
      this.arrayError = [];
    },
    DeleteMarksGrade(id) {
        var funName = "deleteMarksGrade";
        this.props.type = "theinDelete";
        this.props.url = `marksGrade/${funName}/${id}`;
    },
    EditMarksGrade(id) {
      this.axios.get(`/api/marksGrade/editMarksGrade/${id}`).then(response => {
        this.saveMarksGrade = response.data;
      });
    },
     printme(table) {
      Util.printme(table);
    },
    downloadExcel(table, name, filename) {
      Util.downloadExcel(table, name, filename);
    }
  }
};
</script>