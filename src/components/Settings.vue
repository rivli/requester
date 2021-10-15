<template>
  <div>
    <div class="trigger" @click="isShown = true">
      <slot>Settings</slot>
    </div>

    <div v-if="isShown" class="modal">
      <div class="closer" @click="isShown = false" />

      <div class="dialog">
        <div class="title">Settings</div>
        <div>
          <label class="darktheme__label">
            <input type="checkbox" v-model="settings.darktheme" />
            Use dark theme?
          </label>
        </div>
        <br />
        <input type="text" v-model="settings.host" placeholder="Host" />

        <button class="go_btn" @click="save">Save</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Settings",
  props: {
    value: {
      type: Object,
      default: () => ({
        host: "",
        darktheme: false,
      }),
    },
  },
  data: () => ({
    isShown: false,
    settings: {
      host: "",
      darktheme: false,
    },
  }),

  mounted() {
    if (localStorage.getItem("requester")) {
      const requesterSettings = JSON.parse(localStorage.getItem("requester"));

      this.settings = requesterSettings;
    } else this.settings = JSON.parse(JSON.stringify(this.value));
  },

  methods: {
    save() {
      this.$emit("input", JSON.parse(JSON.stringify(this.settings)));
      this.isShown = false;

      const requesterSettings = JSON.stringify(this.settings);
      localStorage.setItem("requester", requesterSettings);
    },
  },
};
</script>

<style lang="scss" scoped>
.trigger {
  cursor: pointer;
  display: inline-block;
}

.modal {
  position: fixed;
  left: 0;
  top: 0;
  height: 100vh;
  width: 100vw;
  z-index: 100;
  display: flex;
  align-items: center;
  justify-content: center;

  .closer {
    position: absolute;
    left: 0;
    top: 0;
    width: 100vw;
    height: 100vh;
    background-color: #c5c1c159;
    backdrop-filter: blur(3px);
    cursor: pointer;
  }

  .dialog {
    background-color: #fff;
    padding: 10px 30px;
    min-width: 70vw;
    min-height: 100px;
    z-index: 1;
  }
}

.darktheme__label {
  display: flex;
  align-items: center;
  cursor: pointer;
}
</style>
