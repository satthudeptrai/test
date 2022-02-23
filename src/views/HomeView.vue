<template>
  <div class="home">
    <button class="btn btn-create" @click="showModal = !showModal">Create</button>
    <Modal
     titleModal="create"
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
          <button class="btn btn-submit" @click="submitForm()">
            Submit
          </button>
        </div>
      </div>
    </Modal>
    <div>
      <table class="table-custom">
        <tr class="header-custom">
          <th>Title</th>
          <th style="width: 200px">Keywords</th>
          <th style="width: 200px">Create</th>
          <th style="width: 500px">Action</th>
        </tr>
        <tr v-for="item in listSort" :key="item.id">
          <td>{{item.title}}</td>
          <td>{{convertKeywords(item.keyword)}}</td>
          <td>{{formatDate(item.created)}}</td>
          <td>
            <div>

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
  status: 0,
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
      sortType: 0
    }
  },
  computed: {
    listSort () {
      let temp = [...this.listData];
      switch(this.sortType) {
        case 0:
          temp.sort((a, b) => {
            const date1 = new Date(a.created);
            const date2 = new Date(b.created);
            return date2.getTime() - date1.getTime();
          });
          break;
      }
      return temp;
    }
  },
  methods: {
    submitForm() {
      this.dataForm.id = Math.random();
      this.dataForm.created = new Date();
      this.listData.push(this.dataForm);
      this.dataForm = {...defaultData};
      this.showModal = false;
    },
    convertKeywords (keyword) {
      const text = this.listKeywords.find(item => item.id === keyword);
      return text ? text.value : ''
    },
    formatDate (date) {
      console.log(date)
      const dateTemp = new Date(date)
      const month = dateTemp.getMonth() + 1;
      const day = dateTemp.getDate();
      const year= dateTemp.getFullYear();
      const hh = dateTemp.getHours();
      const mm = dateTemp.getMinutes();
      const ss = dateTemp.getSeconds();
      return `${day}/${month}/${year} ${hh}:${mm}:${ss}`
    }
  }
}
</script>
<style lang="scss" scoped>
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
  }
  .btn-cancel {
    background: red;
    color: #fff;
    &:hover {
      opacity: 0.6;
    }
  }
  .btn-submit {
    background: rgb(54, 176, 247);
    color: #000;
    margin-left: 10px;
    &:hover {
      opacity: 0.6;
    }
  }
  .btn-create {
    background: rgb(92, 252, 113);
    color: #000;
    margin-bottom: 10px;
    &:hover {
      opacity: 0.6;
    }
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
  }
</style>