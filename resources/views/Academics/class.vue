<template>
    <div class="form" id="bar">
        <div class="toplink">
            <h2 class="stuName">Academics</h2>
            <h4 class="stuLink">
                <router-link to="/dashboard" class="home">Home</router-link>> Class
            </h4>
        </div>
        <hr style="margin-bottom: -0.5rem;" />

        <confirm :url="props"></confirm>
        <Loading></Loading>
        <div class="row rowContainer" style="align-items: end !important;">
            <div class="col-lg-5 col-md-12" style="padding:0;">
                <div class="card">
                    <div class="card-header">
                        <h6>Add Class</h6>
                    </div>
                    <div class="card-body" style="padding:1rem 0;border-bottom: 1px solid #8080808c;">
                        <message :alertmessage="msg" id="alertmsg"/>
                        <form @submit.prevent="goSave">
                            <div class="col-12">
                                <label for="class">
                                    Class
                                    <strong>*</strong>
                                </label>
                                <input id="classid" type="text" class="inputbox" name="class" v-model="ClassObj.class"
                                    @keyup="onValidate(ClassObj.class, 'classid', 'classmsg')"
                                    v-on:blur="onValidate(ClassObj.class, 'classid', 'classmsg')"/>
                                    <span id="classmsg" class="error_message">Class is required</span>
                            </div>
                            <div class="col-12">
                                <label for="class">
                                    Section
                                    <strong>*</strong>
                                </label>
                                <div v-for="Obj in SectionList" :key="Obj.id" class="checkbox">
                                    <label class="checkboxlabel">
                                        <input type="checkbox" class="checkboxinput" value="" v-model="Obj.checked" @click="check($event,Obj)"/> {{Obj.section}}
                                    </label>
                                </div>
                                <span id="classsectionmsg" class="error_message">Section is required</span>
                            </div>
                            <div class="col-12 column-12">
                                <button type="submit" id="globalSave" class="save">Save</button>
                            </div>
                        </form>
                    </div>
                </div>
            </div>

            <div class="col-lg-7 col-md-12" style="padding-left:0;">
                <div class="card">
                    <div class="card-header">
                        <h6>Class List</h6>
                    </div>
                    <div class="card-body">
                    <message :alertmessage="deletemsg" id="delalertmsg"/>
            
                    <input v-on:keyup="searchTable()" id="myInput" type="text" placeholder="Search..." class="searchText"/>
                    <div class="copyRows">
                        <div class="row" id="copyRow">                
                            <div class="col-3">
                                <a href="#" @click.prevent="downloadExcel('studenttable', 'name', 'Class.xls')" title="Excel">
                                    <i class="fa fa-file-excel-o"></i>
                                </a>
                            </div>
                            <div class="col-3">
                                <a href="#" @click.prevent="printme('print')" title="Print">
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
                    <div v-if="ClassList.length == 0">
                        <h1 class="NoData">No Data</h1>
                    </div>
                    <div v-if="ClassList.length != 0" class="table-responsive" id="print">
                        <table class="table table-hover table-striped" id="studenttable">
                            <thead>
                                <tr>
                                    <th class="all" nowrap>Class</th>
                                    <th class="all" nowrap>Sections</th>
                                    <th class="all" style="text-align:right;" nowrap>Action</th>
                                </tr>
                            </thead>
                            <tbody id="myTable">
                                <tr v-for="classes in ClassList" :key="classes.id" class="active">
                                    <td class="all" nowrap>{{classes.class}}</td>
                                    <td class="all" nowrap>
                                        <label v-for="section in classes.section" :key="section.section" style="display: block; margin: 0px;font-size: 13px;font-weight: 200;">{{section.section}}</label>
                                    </td>                                        
                                    <td style="text-align:right;vertical-align:top;">
                                        <i @click="goEdit(classes)" class="fa fa-pencil pen">
                                            <span class="penLabel">Edit</span>
                                        </i>
                                        <i @click="goDelete(classes.id)" data-toggle="modal" data-target="#exampleModalCenter" class="fa fa-trash time">
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
</div>
</template>

<script>
import message from "../Alertmessage/message.vue";
import confirm from "../message/confirm.vue";
import Loading from "../LoadingController.vue";

import { EventBus } from "../../js/event-bus.js";
import {Util} from '../../js/util';

export default {
    components: {
        Loading,
        confirm,
        message
    },
    data() {
        return {   
            ClassObj: { "class":"", "section": []},        
            SectionList: [],
            ClassList: [],
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
            }
        };
    },
    created() {
        EventBus.$emit("ThemeClicked");
        EventBus.$on("clicked", response => {            
            this.deletemsg.text = response.text;
            this.deletemsg.type = response.type;
            Util.workAlert('#delalertmsg');
            this.getAllClass();
        });
        EventBus.$on("SessionSaved", response => {            
            console.log(JSON.stringify(response));
            this.getAllSection();
            this.getAllClass();
        });
        this.getAllSection();
        this.getAllClass();
    },
    mounted() {
        EventBus.$emit("onLoad");
    },
    methods: {
        getAllSection(){
            this.SectionList = [];
            this.axios
                .get('/api/section')
                .then(response => {                        
                    for(let i=0; i < response.data.length; i++){
                        this.SectionList.push({"id": response.data[i].id,"section": response.data[i].section, "checked": false});
                    }            
                });
        },

        getAllClass(){
            EventBus.$emit("onLoad");
            this.ClassList = [];
            this.axios
            .get('/api/class')
            .then(response => {
                let array = response.data.sort((a, b) => {
                    if (a.sectionid > b.sectionid) {
                        return 1;
                    }
                    if (a.sectionid < b.sectionid) {
                        return -1;
                    }
                    return 0;
                });          
                this.sortList(array);
            });
        },

        sortList(aList){       
            for(let i=0; i < aList.length; i++){
                if(this.ClassList == [] || this.ClassList.length == 0){
                    let obj = [];
                    obj.push({"section": aList[i].section});
                    this.ClassList.push({"id": aList[i].classid,"class": aList[i].class, "section": obj});
                }
                else{
                    let check = 0;
                    for(let a=0; a < this.ClassList.length; a++){
                        if(this.ClassList[a].class == aList[i].class){              
                            this.ClassList[a].section.push({"section": aList[i].section});
                            check = 1;
                        }            
                    }

                    if(check == 0)
                    {
                        let obj1 = [];
                        obj1.push({"section": aList[i].section});
                        this.ClassList.push({"id": aList[i].classid,"class": aList[i].class, "section": obj1});
                    }
                }
            }
            EventBus.$emit("onLoadEnd");
        },

        check(e,Obj){
            if(e.target.checked)
            {
                this.ClassObj.section.push(Obj.id);
                $('#classsectionmsg').css('display', 'none');
            }
            else
            {
            this.ClassObj.section.splice(this.ClassObj.section.map(item => item).indexOf(Obj.id), 1);
            if(this.ClassObj.section == []|| this.ClassObj.section.length == 0 || this.ClassObj.section == undefined)
            {          
                $('#classsectionmsg').css('display', 'block')
            }
            }
        },

        onValidate(value, inputId, megId)
        {
            Util.onValidate(value, inputId, megId);            
        },

        checkValidate()
        {
            var returnValue = true;
            if(this.ClassObj.class == "" || this.ClassObj.class == undefined)
            {
                Util.onValidateMessage('classid', 'classmsg');
                returnValue = false;
            }
            if(this.ClassObj.section == []|| this.ClassObj.section.length == 0 || this.ClassObj.section == undefined)
            {          
                $('#classsectionmsg').css('display', 'block');
                returnValue = false;
            }            
            return returnValue;
        },

        goSave(){
            if(this.checkValidate())
            {
                this.axios
                    .post('/api/Class/save', this.ClassObj)
                    .then(response => {                
                        this.getAllClass();                
                        this.ClassObj = {"class":"", "id": ""};
                        this.ClassObj.section = [];
                        this.msg.text = response.data.text;
                        this.msg.type = response.data.type;
                        Util.workAlert('#alertmsg');
                        for(let i=0; i<this.SectionList.length; i++){
                            this.SectionList[i].checked = false;
                        }
                    })
                    .catch(error => {            
                        console.log("err->" + JSON.stringify(this.error.response));
                    });
            }
        },

        goEdit(aObj){
            this.ClassObj.section = [];
            this.ClassObj.class = aObj.class;
            this.ClassObj.id = aObj.id;
            let editObj = [];
            for(let i = 0 ; i < this.SectionList.length; i++){
                let c = 0;
                for(let a=0; a < aObj.section.length; a++){
                    if(this.SectionList[i].section == aObj.section[a].section){
                        editObj.push({"id": this.SectionList[i].id, "section": this.SectionList[i].section, "checked": true});
                        this.ClassObj.section.push(this.SectionList[i].id);
                        c = 1;
                    }
                }

                if(c == 0){
                    editObj.push({"id": this.SectionList[i].id, "section": this.SectionList[i].section, "checked": false});
                }
            }      
            this.SectionList = [];
            this.SectionList = editObj;
        },

        goDelete(aID){
            var funName = "delete"; /**Delete function */
            this.props.type = "delete";
            this.props.url = `Class/${funName}/${aID}`;
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

        printme(table)
        {
            Util.printme(table);
        },

        downloadExcel(table, name, filename) 
        {
            Util.downloadExcel(table,name,filename);
        }
    }
};
</script>