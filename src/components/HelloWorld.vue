<template>
  <v-container>
    <v-row class="text-center">
      <v-col class="mb-5" cols="12">
        <h2 class="headline font-weight-bold mb-3">
          Add Icons
        </h2>

        <div class="my-2">
          <v-btn color="primary" @click="$refs.file.click()">Upload</v-btn>
          <input
            type="file"
            ref="file"
            id="file-upload"
            style="display:none"
            @change="onFileChange"
            multiple
          />
        </div>
      </v-col>

      <v-col class="mb-5" cols="12">
        <h2 class="headline font-weight-bold mb-3">
          List Icons
        </h2>

        <h3>Drag</h3>
        <draggable
          :list="icons"
          :disabled="!enabled"
          ghost-class="ghost"
          :move="checkMove"
          @start="dragging = true"
          @end="dragging = false"
          v-bind="dragOptions"
        >
          <transition-group
            type="transition"
            :name="!dragging ? 'flip-list' : null"
            class="row"
          >
            <div
              v-for="icon in icons"
              v-bind:key="icon.id"
              class="list-group list-group-item col col-2"
            >
              <v-card max-width="400">
                <div style="padding: 10px; background-color: #123541">
                  <v-img
                    class="white--text align-end"
                    height="200px"
                    :src="icon.url"
                    contain
                    aspect-ratio="1"
                  >
                  </v-img>
                </div>
                <v-card-subtitle>
                  <a
                    :id="'icon-filename-' + icon.id"
                    @click="copyLabel('icon-filename-' + icon.id)"
                    >{{ icon.filename }}</a
                  >
                </v-card-subtitle>
              </v-card>
            </div>
          </transition-group>
        </draggable>
      </v-col>
    </v-row>
    <v-snackbar v-model="showSnackbar" :timeout="2000" color="success">
      {{ snackbar }}
    </v-snackbar>
  </v-container>
</template>

<script lang="ts">
import Vue from "vue";
import { v4 as uuidv4 } from "uuid";
import draggable from "vuedraggable";

interface IconDataIntarface {
  id: number | string;
  filename: string;
  size: number;
  url: string;
}

export default Vue.extend({
  name: "HelloWorld",
  components: {
    draggable
  },

  data: () => ({
    icons: [] as IconDataIntarface[],
    snackbar: "",
    showSnackbar: false,
    enabled: true,
    dragging: false
  }),
  computed: {
    dragOptions() {
      return {
        animation: 200,
        group: "description",
        disabled: false,
        ghostClass: "ghost"
      };
    }
  },
  methods: {
    onFileChange: function(event: Event) {
      const target = event.target as HTMLInputElement;
      const files: IconDataIntarface[] = Array.from(target.files || []).map(
        file => ({
          id: uuidv4(),
          filename: file.name,
          size: file.size,
          url: URL.createObjectURL(file)
        })
      );
      this.icons.push(...files);
    },
    copyLabel: function(id: string) {
      const element = document.getElementById(id);
      const selection = window.getSelection();

      if (!selection) return;
      if (!element) return;

      const range = document.createRange();
      range.selectNodeContents(element);
      selection.removeAllRanges();
      selection.addRange(range);
      document.execCommand("copy");
      selection.removeAllRanges();

      this.snackbar = `Copy ${element.innerText} to clipboard`;
      this.showSnackbar = true;
    }
  }
});
</script>

<style scoped>
.button {
  margin-top: 35px;
}
.flip-list-move {
  transition: transform 0.5s;
}
.no-move {
  transition: transform 0s;
}
.ghost {
  opacity: 0.5;
  background: #c8ebfb;
}
.list-group {
  min-height: 20px;
}
.list-group-item {
  cursor: move;
}
.list-group-item i {
  cursor: pointer;
}
</style>
