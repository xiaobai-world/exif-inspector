<template>
 <div class="exif-inspector-app">
  <label class="select">
   <input type="file" @input="read" />
   <span class="tip">Select Photo</span>
   <img class="img" :id="id" />
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
  const id = `preview-${Math.random().toString(36).substring(2)}`;

  return {
   tags,
   img,
   id,
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

<style lang="less">
.exif-inspector-app {
 font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
  Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
 .select {
  margin: 25px auto;
  display: flex;
  width: 180px;
  height: 180px;
  border: solid 1px #2dbac1;
  background: #fff;
  position: relative;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  overflow: hidden;
  font-size: 23px;
  box-shadow: 4px 7px 18px rgb(0 0 0 / 17%);
  color: #4ebcd6;
  font-weight: 200;
  input {
   opacity: 0;
   position: absolute;
  }

  .tip {
   position: absolute;
   left: 0;
   width: 100%;
   text-align: center;
   top: 50%;
   transform: translateY(-50%);
  }
  img {
   max-width: 100%;
   position: relative;
   z-index: 2;
  }
 }
 table {
  width: 80%;
  margin: 0 auto;
  table-layout: fixed;
  border-collapse: collapse;
  border-spacing: 0;
  th,
  td {
   border: solid 1px rgb(235, 235, 235);
   padding: 0.9em 0.5em;
   font-weight: 200;
  }
  th {
   text-align: right;
  }
  tr:hover {
   background: #f9f9f9;
  }
 }
}
</style>