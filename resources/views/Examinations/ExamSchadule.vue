<template>
  <div class="form" id="bar">
    <div
      class="modal fade"
      id="exampleModalCenter"
      tabindex="-1"
      role="dialog"
      aria-labelledby="exampleModalCenterTitle"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-dialog-centered" role="document" style="width:100% !important;padding-right: 17px;">
        <div class="modal-content" style="width:100% !important;">
          <div class="modal-body" style="padding:0;">
            <div class="card-header" style="width: 100%;margin-left: 0;">
              <h6>Exam {{this.examName}}</h6>
            </div>
            <div style="padding:1rem;" >
              <div style="height:20px;font-size:20px;">{{Class_Name}} = {{Section_name}}</div>
              <div class="copyRows">
          <div class="row" id="copyRow" style="margin-top: -20px;">
            <div class="col-3">
              <a href="#" title="Excel" @click.prevent="downloadExcel('examSchaduleTable', 'name', 'Marks.xls')">
                <i class="fa fa-file-excel-o"></i>
              </a>
            </div>
            <div class="col-3">
              <a href="#" title="Print" @click.prevent="printme('print2')">
                <i class="fa fa-print"></i>
              </a>
            </div>
            <div class="col-3">
              <a href="#" title="Columns">
                <i class="fa fa-columns"></i>
              </a>
            </div>
          </div>
        </div>
              <div class="table-responsive table-striped" style="padding:0;" id="print2">
                <table class="table table-hover" id="examSchaduleTable">
                  <thead>
                    <th nowrap>Subject</th>
                    <th nowrap>Date</th>
                    <th nowrap>Start Time</th>
                    <th nowrap>End Time</th>
                    <th nowrap>Room</th>
                    <th nowrap>Full Marks</th>
                    <th nowrap>Passing Marks</th>
                  </thead>
                  <tbody>
                    <tr v-for="ReceiveExamData in receiveExamData" :key="ReceiveExamData.id">
                      <td
                        nowrap
                        v-if="ReceiveExamData.date != null"
                      >{{ReceiveExamData.subject}}(Th:)</td>
                      <td nowrap v-if="ReceiveExamData.date != null">{{ReceiveExamData.date}}</td>
                      <td nowrap v-if="ReceiveExamData.date != null">{{ReceiveExamData.start_time}}</td>
                      <td nowrap v-if="ReceiveExamData.date != null">{{ReceiveExamData.end_time}}</td>
                      <td nowrap v-if="ReceiveExamData.date != null">{{ReceiveExamData.room}}</td>
                      <td nowrap v-if="ReceiveExamData.date != null">{{ReceiveExamData.full_marks}}</td>
                      <td
                        nowrap
                        v-if="ReceiveExamData.date != null"
                      >{{ReceiveExamData.passing_marks}}</td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="save" id="globalSave" data-dismiss="modal">Close</button>
          </div>
        </div>
      </div>
    </div>

    <div class="toplink">
      <h2 class="stuName">Examinations</h2>
      <h4 class="stuLink">
        <router-link to="/dashboard" class="home">Home</router-link>> Examinations
      </h4>
    </div>
    <hr />
  <Loading></Loading>
    <div class="card">
      <div class="card-header">
        <h6>Examinations</h6>
        <router-link to="/saveexamschdule" class="add">Add</router-link>
      </div>
      <div class="card-body">
        <message :alertmessage="msg" id="alertmsg" />
        <div class="row" id="row">
          <div class="col-lg-6 col-md-6 col-sm-6 textbox">
            <label>
              Class
              <strong style="color:var(--danger)">*</strong>
            </label>
            <br />
            <select class="inputbox" @change="getSection($event)">
              <option value>Select</option>
              <option :value="sclass.id" v-for="sclass in Class" :key="sclass.id">{{ sclass.class }}</option>
            </select>
          </div>
          <div class="col-lg-6 col-md-6 col-sm-6 textbox">
            <label>
              Section
              <strong style="color:var(--danger)">*</strong>
            </label>
            <br />
            <select class="inputbox" @change="getSectionId($event)">
              <option value>Select</option>
              <option
                :value="Ssection.id"
                v-for="Ssection in Sections"
                :key="Ssection.id"
              >{{ Ssection.section }}</option>
            </select>
          </div>
          <div class="col-12 column-12">
            <button class="searchButton" id="globalSearch" @click="Search(id1,id2)">Search</button>
          </div>
        </div>
      </div>

      <div class="sub-header" v-if="display == true">
        <h6>Exam List</h6>
      </div>
      <div class="card-body" v-if="display == true">
        <input
          type="text"
          id="myInput"
          v-on:keyup="searchTable()"
          placeholder="Search..."
          class="searchText"
        />
        <div class="copyRows">
          <div class="row" id="copyRow">
            <div class="col-3">
              <a href="#" title="Excel" @click.prevent="downloadExcel('studenttable', 'name', 'Marks.xls')">
                <i class="fa fa-file-excel-o"></i>
              </a>
            </div>
            <div class="col-3">
              <a href="#" title="Print" @click.prevent="printme('print')">
                <i class="fa fa-print"></i>
              </a>
            </div>
            <div class="col-3">
              <a href="#" title="Columns">
                <i class="fa fa-columns"></i>
              </a>
            </div>
          </div>
        </div>
        <div v-if="data == false">
          <h1 class="NoData">No Data</h1>
        </div>
        <div class="table-responsive" v-if="data == true" id="print">
          <table class="table table-hover table-striped" id="studenttable">
            <thead>
              <tr>
                <th class="all" nowrap>No</th>
                <th class="all" nowrap>Exam</th>
                <th class="all" style="text-align:right;" nowrap>Action</th>
              </tr>
            </thead>
            <tbody id="myTable">
              <tr class="active" v-for="(examnames,index) in examNames" :key="examnames.id">
                <td>{{index + 1}}</td>
                <td class="all" nowrap>
                  <p class="toolText" v-if="examnames.is_active == 'yes'">
                    {{examnames.name}}
                    <span class="tooltipLabel">{{examnames.remark}}</span>
                  </p>
                  <p class="toolText" v-else></p>
                </td>
                <td style="text-align:right;">
                  <router-link
                    :to="{name: 'editExamSchadule', params: { exam_id: examnames.id , exam_name:examnames.name , class_id:id1 , section_id:id2 , class_name:Class_Name , section_name:Section_name}}"
                    style="color:white;margin-left:5px;"
                  >
                    <i class="fa fa-pencil pen">
                      <span class="penLabel" >Edit</span>
                    </i>
                  </router-link>
                  <button
                    v-if="examnames.is_active == 'yes'"
                    class="viewButton btn-sm"
                    id="globalView"
                    data-toggle="modal"
                    data-target="#exampleModalCenter"
                    @click="GetExamData(id1,id2,examnames.id,examnames.name)"
                  >View</button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
    <!-- <button type="button" @click="Test()">Test</button> -->
  </div>
</template>
<script>
import { EventBus } from "../../js/event-bus.js";
import store from "store2";
import message from '../Alertmessage/message.vue';
import { Util } from "../../js/util";
import Loading from "../LoadingController.vue";
export default {
  components: {
          message,
          Loading
        },
  data() {
    
    return {
      Class: [],
      Sections: [],
      id1: "",
      id2: "",
      array: [],
      Class_Name: "Class",
      Section_name: "Section",
      examNames: [],
      examName: "",
      getExamData: [],
      receiveExamData: [],
      display: false,
      data: false,
      msg: {
        text: "",
        type: ""
      },
      TestArray: []
    };
  },
  mounted(){
    EventBus.$emit("onLoad");
    this.getClass();
  },
  created() {
    EventBus.$emit("ThemeClicked");
    var message = store.get("msg");
    if (message != null) {
      if (message == "save") {
        this.msg.text = "Exam Schadule added successfully";
        this.msg.type = "success";
      } else {
        this.msg.text = "Exam Schadule updated successfully";
        this.msg.type = "success";
      }
      Util.workAlert("#alertmsg");
    }
    store.clearAll();
  },
  methods: {
    getClass() {
      this.axios.get(`/api/getClasses`).then(response => {
        this.Class = response.data;
        EventBus.$emit("onLoadEnd");
      });
    },
    getSection(event) {
      this.display = false;
      this.axios
        .get(`/api/getClassSection/${event.target.value}`)
        .then(response => {
          this.Sections = response.data;
          this.id1 = event.target.value;
          
        });
    },
    getSectionId(eventS) {
      this.display = false;
      this.id2 = eventS.target.value;
    },
    Search(Class_id, Section_id) {
      EventBus.$emit("onLoad");
      this.array.push(Class_id);
      this.array.push(Section_id);
      this.axios.get(`/api/getClassSectionId/${this.array}`).then(response => {
        var ReturnData = response.data;
        var RealData = [];

        for (var i = 0; i < ReturnData.length; i++) {
          for (var ii = 0; ii < RealData.length; ii++) {
            if (ReturnData[i].name == RealData[ii].name) {
              let iii = RealData.map(item => item.name).indexOf(
                ReturnData[i].name
              );
              RealData.splice(iii, 1);
            }
          }
          RealData.push(ReturnData[i]);
        }
        this.examNames = RealData;
      });
      this.axios
        .get(`/api/examSchadules/getClassName/${Class_id}`)
        .then(response => {
          this.Class_Name = response.data;
        });
      this.axios
        .get(`/api/examSchadules/getSectionName/${Section_id}`)
        .then(response => {
          this.Section_name = response.data;
          EventBus.$emit("ThemeClicked");
        var dataValue;
        for (var i = 0; i < this.examNames.length; i++) {
          dataValue = this.examNames[i].name;
        }
        if (dataValue == undefined || dataValue == null || dataValue == "") {
          this.data = false;
        } else {
          this.data = true;
        }
        this.display = true;
        EventBus.$emit("onLoadEnd");
        });
      this.array = [];
    },
    GetExamData(class_id, section_id, exam_id, exam_name) {
      EventBus.$emit("onLoad");
      this.getExamData.push(class_id);
      this.getExamData.push(section_id);
      this.getExamData.push(exam_id);
      this.examName = exam_name;

      this.axios
        .get(`/api/examSchadules/getExamData/${this.getExamData}`)
        .then(response => {
          this.receiveExamData = response.data;
          EventBus.$emit("onLoadEnd");
        });
      this.getExamData = [];
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
    printme(table) {
      Util.printme(table);
    },
    downloadExcel(table, name, filename) {
      Util.downloadExcel(table, name, filename);
    }
  }
};
</script>