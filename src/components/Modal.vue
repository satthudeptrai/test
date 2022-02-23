<template>
    <div
     class="modal"
     :style="{
        display: isShow ? 'block' : 'none'
     }"
    >
        <div class="modal-content" :style="{width: widthModal}">
            <div class="modal-header">
                <span class="close" @click="closeFrom()">&times;</span>
                <div>{{titleModal}}</div>
            </div>
            <div class="modal-body">
                <slot></slot>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'modalCustom',
    props: {
        isShow: {
            require: true,
            type: Boolean
        },
        titleModal: {
            require: true,
            type: String
        },
        widthModal: {
            require: false,
            type: String,
            default: '80%'
        }
    },
    methods: {
        closeFrom() {
            this.$emit('closeFrom');
        }
    }
}
</script>

<style lang="scss" scoped>
.modal {
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgb(0,0,0);
  background-color: rgba(0,0,0,0.4);
  padding-top: 100px;
  .modal-content {
    position: relative;
    background-color: #fefefe;
    margin: auto;
    padding: 0;
    border: 1px solid #888;
    box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2),0 6px 20px 0 rgba(0,0,0,0.19);
    .modal-header {
        padding: 2px 16px;
        background-color: #202c38;
        color: white;
        text-align: left;
        font-size: 25px;
        .close {
            color: #aaa;
            float: right;
            font-size: 28px;
            font-weight: bold;
        }
        .close:hover{
            text-decoration: none;
            cursor: pointer;
        }
    }
    .modal-body {
        padding: 10px 16px;
        text-align: left;
    }
  }
}
</style>