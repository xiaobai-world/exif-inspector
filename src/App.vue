<template>
  <div>
    <label class="select">
      <input type="file" @change="read" />
      Select Photo
      <img class="img" />
    </label>
    <table>
      <template v-for="(tag, key) in tags">
        <tr v-if="key && tag.description" :key="key">
          <th>{{ key }}</th>
          <td>{{ tag.description }}</td>
        </tr>
      </template>
    </table>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import ExifReader from "exifreader";

interface HTMLInputEvent extends Event {
  target: HTMLInputElement & EventTarget;
}

export default defineComponent({
  name: "App",
  setup() {
    const img = ref<HTMLImageElement>();

    const tags = ref({});

    return {
      tags,
      img,
    };
  },
  methods: {
    async read(event: HTMLInputEvent) {
      let files = event.target.files;
      if (!files || !files[0]) {
        return;
      }
      const file = files[0];

      var reads = new FileReader();
      reads.readAsDataURL(file);

      reads.addEventListener("load", () => {
        this.$el.querySelector("img").src = reads.result;
      });

      this.tags = await ExifReader.load(file);
    },
  },
});
</script>

<style scoped lang="less">
.select {
  margin: 25px auto;
  display: flex;
  width: 180px;
  height: 180px;
  border: solid 1px red;
  background: #fff;
  position: relative;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  overflow: hidden;
  font-size: 23px;
  input {
    opacity: 0;
    position: absolute;
  }
}
div > table {
  width: 100%;
  table-layout: fixed;
  border: solid 1px red;
  border-collapse: collapse;
  border-spacing: 0;
  th,
  td {
    border: solid 1px #aaa;
    padding: 0.4em;
  }
  th {
    text-align: right;
  }
}
</style>