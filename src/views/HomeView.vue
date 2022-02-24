<template>
  <div class="home">
    <button class="btn btn-create" @click="create()">Create</button>
    <div class="tabs">
      <div class="tab" :class="{active: tab === 1}" @click="tab=1">
        All data
      </div>
      <div class="tab" :class="{active: tab === 2}" @click="tab=2">
        Liked data
      </div>
      <div class="tab" :class="{active: tab === 3}" @click="tab=3">
        Removed data
      </div>
    </div>
    <Modal
     :titleModal="isCreate? 'create' : 'edit'"
     widthModal="600px"
     :isShow="showModal"
     @closeFrom="showModal = false"
    >
      <div>
        <div class="layer">
          <div class="label-input">
            Title
          </div>
          <div>
            <input type="text" class="txt-input" v-model="dataForm.title">
          </div>
        </div>
        <div class="layer">
          <div class="label-input">
            Keywords
          </div>
          <div>
            <select class="select-input" v-model="dataForm.keyword">
              <option v-for="item in listKeywords" :key="item.id" :value="item.id">
                {{item.value}}
              </option> 
            </select>
          </div>
        </div>
        <div class="layer">
          <div class="label-input">
            Description
          </div>
          <div>
            <textarea class="area-input" rows="4" v-model="dataForm.descript">
            </textarea>
          </div>
        </div>
        <div class="btn-group">
          <button class="btn btn-cancel" @click="showModal = false">
            Cancel
          </button>
          <button class="btn btn-submit" :disabled="!dataForm.title" @click="submitForm()">
            Submit
          </button>
        </div>
      </div>
    </Modal>
    <div class="search-form">
      <input type="text" class="txt-search" v-model="search">
      <div v-show="search" class="clear" @click="search = ''">
        x
      </div>
    </div>
    <div>
      <table class="table-custom">
        <tr class="header-custom">
          <th>
            Title
            <div style="float: right">
              <div class="icon-custom" :style="{color: sortType === 2 ? '#bdb1b1' : '#000'}" @click="sortType = 2">
                &#9650;
              </div>
              <div class="icon-custom" :style="{color: sortType === 3 ? '#bdb1b1' : '#000'}" @click="sortType = 3">
                &#9660;
              </div>
            </div>
          </th>
          <th style="width: 200px">Keywords</th>
          <th style="width: 200px">
            Create
            <div style="float: right">
              <div class="icon-custom" :style="{color: sortType === 1 ? '#bdb1b1' : '#000'}" @click="sortType = 1">
                &#9650;
              </div>
              <div class="icon-custom" :style="{color: sortType === 0 ? '#bdb1b1' : '#000'}" @click="sortType = 0">
                &#9660;
              </div>
            </div>
          </th>
          <th style="width: 500px">Action</th>
        </tr>
        <tr v-for="item in listSort" :key="item.id">
          <td>{{item.title}}</td>
          <td>{{convertKeywords(item.keyword)}}</td>
          <td>{{formatDate(item.created)}}</td>
          <td>
            <div class="action">
              <div :style="{color: item.status === '1' ? 'red' : '#ff000063'}" @click="changeStatus(item.id, '1')">
                like
              </div>
              <div :style="{color: item.status === '2' ? 'red' : '#ff000063'}" @click="changeStatus(item.id, '2')">
                unlike
              </div>
              <div v-show="tab!==3" class="action2" @click="remove(item.id)">
                remove
              </div>
              <div v-show="tab===3" class="action2" @click="undo(item.id)">
                undo
              </div>
              <div class="action2" @click="edit(item)">
                edit
              </div>
            </div>
          </td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import Modal from '@/components/Modal.vue'
import {listKeywords, dummyData} from '@/utils/common'
const defaultData = {
  id: '',
  title: '',
  keyword: '1',
  descript: '',
  status: '0',
  isDelete: false,
  created: ''
}
export default {
  name: 'HomeView',
  components: {
    Modal
  },
  data() {
    return {
      showModal: false,
      listKeywords: listKeywords,
      dataForm: {...defaultData},
      listData: dummyData,
      sortType: 0,
      tab: 1,
      isCreate: true,
      search: ''
    }
  },
  computed: {
    listSort () {
      let tempSort = [...this.listData];
      switch(this.sortType) {
        case 0:
          tempSort.sort((a, b) => {
            const date1 = new Date(a.created);
            const date2 = new Date(b.created);
            return date2.getTime() - date1.getTime();
          });
          break;
        case 1:
          tempSort.sort((a, b) => {
            const date1 = new Date(a.created);
            const date2 = new Date(b.created);
            return date1.getTime() - date2.getTime();
          });
          break;
        case 2:
          tempSort.sort((a, b) => {
            if (a > b) {
              return 1;
            }
            if (a.title < b.title) {
              return -1
            }
            return 0
          });
          break;
        case 3:
          tempSort.sort((a, b) => {
            if (a.title > b.title) {
              return -1;
            }
            if (a.title < b.title) {
              return 1
            }
            return 0
          });
          break;
        default:
          break;
      }
      let tempList = []
      switch(this.tab) {
        case 1:
          tempList = tempSort.filter(item => !item.isDelete);
          break;
        case 2:
          tempList = tempSort.filter(item => !item.isDelete && item.status === '1');
          break;
        case 3:
          tempList = tempSort.filter(item => item.isDelete);
          break;
        default:
          break;
      }
      if (this.search) {
        let searchList = tempList.filter(item => item.title.includes(this.search));
        return searchList;
      }
      return tempList;
    }
  },
  mounted() {
    const listTemp = localStorage.getItem("listStorage");
    if (listTemp) {
      this.listData = JSON.parse(listTemp);
    }
  },
  methods: {
    create() {
      this.showModal = true;
      this.isCreate = true;
      this.dataForm = {...defaultData};
    },
    edit(item) {
      this.showModal = true;
      this.isCreate = false;
      this.dataForm = {...item};
    },
    submitForm() {
      if (this.isCreate) {
        this.dataForm.id = Math.random();
        this.dataForm.created = new Date();
        this.listData.push(this.dataForm);
      } else {
        const index = this.listData.findIndex(item => item.id === this.dataForm.id);
        if (index >= 0) {
          this.$set(this.listData, index, this.dataForm);
        }
      }
      this.dataForm = {...defaultData};
      this.showModal = false;
      localStorage.setItem("listStorage", JSON.stringify(this.listData));
    },
    convertKeywords (keyword) {
      const text = this.listKeywords.find(item => item.id === keyword);
      return text ? text.value : ''
    },
    formatDate (date) {
      const dateTemp = new Date(date)
      const month = dateTemp.getMonth() + 1;
      const day = dateTemp.getDate();
      const year= dateTemp.getFullYear();
      const hh = dateTemp.getHours();
      const mm = dateTemp.getMinutes();
      const ss = dateTemp.getSeconds();
      return `${day}/${month}/${year} ${hh}:${mm}:${ss}`
    },
    changeStatus(id, status) {
      const index = this.listData.findIndex(item => item.id === id);
      if (index >= 0) {
        this.listData[index].status = status;
        localStorage.setItem("listStorage", JSON.stringify(this.listData));
      }
    },
    remove(id) {
      const index = this.listData.findIndex(item => item.id === id);
      if (index >= 0) {
        this.listData[index].isDelete = true;
        localStorage.setItem("listStorage", JSON.stringify(this.listData));
      }
    },
    undo(id) {
      const index = this.listData.findIndex(item => item.id === id);
      if (index >= 0) {
        this.listData[index].isDelete = false;
        localStorage.setItem("listStorage", JSON.stringify(this.listData));
      }
    }
  }
}
</script>
<style lang="scss" scoped>
  .search-form {
    position: relative;
    margin-bottom: 10px;
    .txt-search {
      padding: 5px 0;
      border: none;
      border-bottom: solid 1px gray;
      outline: none;
      font-size: 16px;
      width: 400px;
    }
    .clear {
      position: absolute;
      top: 8px;
      left: 390px;
      cursor: pointer;
      font-weight: bold;
    }
  }
  .label-input {
    font-size: 14px;
    margin-bottom: 5px;
  }
  .txt-input {
    padding: 5px 0;
    border: none;
    border-bottom: solid 1px gray;
    outline: none;
    font-size: 16px;
    width: 100%;
  }
  .select-input {
    padding: 5px 10px 5px 0px;
    outline: none;
    border: none;
    border-bottom: solid 1px gray;
    height: 29px;
  }
  .area-input {
    outline: none;
    width: calc(100% - 4px);
    font-size: 16px;
    font-family: Avenir, Helvetica, Arial, sans-serif;
    padding: 1px;
  }
  .layer {
    margin-top: 10px;
  }
  .btn-group {
    display: flex;
    justify-content: flex-end;
  }
  .btn {
    padding: 8px 15px;
    font-size: 18px;
    border: none;
    border-radius: 5px;
    &:hover {
      opacity: 0.6;
    }
  }
  .btn-cancel {
    background: red;
    color: #fff;
  }
  .btn-submit {
    background: rgb(54, 176, 247);
    color: #000;
    margin-left: 10px;
    &:hover {
      opacity: 0.6;
    }
  }
  button {
    &:disabled {
      opacity: 0.3;
      &:hover {
        opacity: 0.3;
      }
    }
  }
  .btn-create {
    background: rgb(92, 252, 113);
    color: #000;
    margin-bottom: 10px;
  }
  .table-custom {
    border-collapse: collapse;
    width: 100%;
    .header-custom {
      text-align: left;
    }
    tr:nth-child(even) {
      background-color: #dddddd;
    }
    td, th {
      border: 1px solid #dddddd;
      text-align: left;
      padding: 8px;
    }
    .icon-custom {
      font-size: 10px;
      margin-left: 8px;
      cursor: pointer;
    }
    .action {
      display: flex;
      div {
        margin-right: 18px;
        cursor: pointer;
        font-weight: bold;
      }
      .action2 {
        color: rgb(17, 13, 241);
      }
    }
  }
  .tabs {
    display: flex;
    margin-bottom: 10px;
    padding-bottom: 5px;
    .tab {
      margin-right: 10px;
      cursor: pointer;
    }
    .active {
      text-decoration: underline;
      font-weight: bold;
      color: blue;
    }
  }
</style>