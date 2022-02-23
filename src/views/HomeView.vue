<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <button @click="showModal = !showModal">Open Modal</button>
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
          <button class="btn btn-cancel">
            Cancel
          </button>
          <button class="btn btn-submit">
            Submit
          </button>
        </div>
      </div>
    </Modal>
  </div>
</template>

<script>
// @ is an alias to /src
import Modal from '@/components/Modal.vue'
import {listKeywords} from '@/utils/common'
export default {
  name: 'HomeView',
  components: {
    Modal
  },
  data() {
    return {
      showModal: false,
      listKeywords: listKeywords,
      dataForm: {
        title: '',
        keyword: '1',
        descript: '',
        status: 0,
        isDelete: false,
        created: ''
      }
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
  }
</style>