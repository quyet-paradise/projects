<template>
  <div class="wrapper-popup"
    v-bind:class="getClassPopup">
    <div class="rule">
      <h3>Luật chơi</h3>
      <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Commodi repudiandae accusantium exercitationem doloremque adipisci. Illum quos magnam facilis inventore mollitia quis quidem vitae? Excepturi doloremque enim, animi tempore deleniti quidem!</p>
      <button 
        class="confirm"
        v-on:click="confirm">Đã Hiểu</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "popup-rule",
  props: {
      isOpenPopup: {
          type: Boolean,
          default: false
      }
  },
  data() {
    return {

    };
  }, 
  methods: {
     confirm() {
        console.log('confirm PopupRule.vue');
        this.$emit('handleConfirm');
     } 
  },
  computed: {
      getClassPopup() {
          return {
              'open-popup': this.isOpenPopup
          }
      }
  }
};
</script>

<style>
    .wrapper-popup {
        position: fixed;
        top: 0;
        left: 0; 
        right: 0;
        bottom: 0;
        background-color: rgba(0, 0, 0, 0.5);
        opacity: 0;
        visibility: hidden;
        transition: all .3s ease;
    }

    .wrapper-popup.open-popup {
        opacity: 1;
        visibility: visible;
    }

    .rule {
        position: absolute;
        background-color: #fff;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%) scale(1.2);
        width: 400px;
        padding: 25px;
        font-family: Arial, Helvetica, sans-serif;
        transition: all .3s ease;
    }

    .open-popup .rule {
        transform: translate(-50%, -50%) scale(1.0);
    }

    .rule h3 {
        margin-bottom: 15px;
    }

    .rule .confirm {
        border: 2px solid #333;
        padding: 5px 15px;
        cursor: pointer;
        background-color: #fff;
        margin-top: 20px;
        transition: all .4s ease;
    }

    .rule .confirm:hover {
        background-color: #333;
        color: #fff;
    }
</style>