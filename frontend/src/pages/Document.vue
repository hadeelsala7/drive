<template>
  <div class="flex justify-between items-center mb-4">
    <input
      v-model="title"
      :placeholder="title"
      class="basis-auto border-0 text-2xl font-normal focus:outline-0 bg-inherit" />
    <div>
      <Button
        class="flex-none mr-1"
        appearance="primary"
        @click="
          $resources.updateDocument.submit({
            title: title,
            content: content,
            entity_name: entityName,
          })
        ">
        Save
      </Button>
      <Button @click="$router.go(-1)">Back</Button>
    </div>
  </div>
  <TextEditor id="editorElem" v-model="content" :fixed-menu="true" />
</template>

<script>
import { Button } from "frappe-ui";
import TextEditor from "@/components/DocEditor/TextEditor.vue";
export default {
  components: {
    TextEditor,
    Button,
  },
  props: {
    entityName: {
      type: String,
      required: false,
      default: "",
    },
  },
  data() {
    return {
      title: null,
      content: null,
    };
  },
  computed: {
    currentFolderID() {
      return this.$store.state.currentFolderID;
    },
  },
  mounted() {
    this.entityName ? this.$resources.getDocument.fetch() : null;
  },
  resources: {
    updateDocument() {
      return {
        url: "drive.api.files.save_doc",
        params: {
          entity_name: this.entityName,
          title: this.title,
          content: this.content,
        },
        onSuccess(data) {
          this.title = data.title;
          this.content = JSON.parse(data.content);
        },
        onError(data) {
          console.log(data);
        },
        auto: false,
      };
    },
    getDocument() {
      return {
        url: "drive.api.files.get_doc_content",
        params: {
          entity_name: this.entityName,
        },
        onSuccess(data) {
          this.title = data.title;
          this.content = JSON.parse(data.content);
        },
        onError(data) {
          console.log(data);
        },
        auto: false,
      };
    },
  },
};
</script>
