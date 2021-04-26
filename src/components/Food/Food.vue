<template>
  <div
    class="pacman__food"
    v-bind:style="positionConvert"
    v-bind:class="ateClass"
  >
    <div class="effective-food"></div>
  </div>
</template>
<script>
export default {
  name: "Food",
  data: function () {
    return {
      foodSize: 60,
      hidden: false,
      key: 0,
    };
  },
  props: {
    position: {
      type: Object,
    },
    uniqueKey: {
      type: String,
    },
  },
  mounted: function () {
    this.key = this.$props.uniqueKey;
  },
  methods: {
    ate: function () {
      this.hidden = true;
    },
  },
  computed: {
    ateClass: function () {
      return {
        hidden: this.hidden ? true : false,
      };
    },
    positionConvert: function () {
      return {
        top: this.$props.position.top + "px",
        left: this.$props.position.left + "px",
      };
    },
  },
};
</script>

<style scoped>
.pacman__food {
  width: 60px;
  height: 60px;
  position: absolute;
}
.effective-food {
  border-radius: 50px;
  width: 20px;
  height: 20px;
  background-color: red;
  margin: 20px;
  background: url("./hamburger.png");
  background-size: 100%;
}
.hidden {
  display: none;
}
</style>