<template>
  <v-container>
    <v-row class="text-center">
      <v-col class="mb-5" cols="12">
        <h2 class="headline font-weight-bold mb-3">
          Add Icons
        </h2>

        <div class="my-2">
          <v-btn color="primary" @click="$refs.file.click()">Add Icons</v-btn>
          <input
            type="file"
            ref="file"
            id="file-upload"
            style="display:none"
            @change="onFileChange"
            multiple
          />
        </div>

        <div class="text-center">
          <v-menu :close-on-content-click="false" offset-x z-index="100">
            <template v-slot:activator="{ on }">
              <v-btn :color="backgroundColor" dark v-on="on">
                Background
              </v-btn>
            </template>

            <v-color-picker v-model="backgroundColor"></v-color-picker>
          </v-menu>
        </div>
      </v-col>

      <v-col class="mb-5" cols="12">
        <h2 class="headline font-weight-bold mb-3">
          List Icons
        </h2>

        <v-text-field
          v-model="search"
          clearable
          flat
          solo-inverted
          hide-details
          label="Search"
        ></v-text-field>
        <draggable
          :list="icons"
          :disabled="!enabled"
          ghost-class="ghost"
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
              v-for="icon in filteredIcons"
              v-bind:key="icon.id"
              class="list-group list-group-item col col-xl-1 col-lg-2 col-md-3 col-sm-4"
            >
              <v-card max-width="400">
                <div
                  v-bind:style="{
                    padding: '10px',
                    backgroundColor: backgroundColor
                  }"
                >
                  <v-img
                    class="white--text align-end"
                    height="100px"
                    :src="icon.url"
                    contain
                    aspect-ratio="1"
                  >
                  </v-img>
                </div>
                <v-card-text
                  @click="copyLabel('icon-filename-' + icon.id)"
                  class="card-text"
                >
                  <p :id="'icon-filename-' + icon.id">
                    {{ icon.filename }}
                  </p>
                </v-card-text>
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
    dragging: false,
    backgroundColor: "#dddddd",
    search: ""
  }),
  computed: {
    dragOptions() {
      return {
        animation: 200,
        group: "description",
        disabled: false,
        ghostClass: "ghost"
      };
    },
    filteredIcons() {
      if (this.search) {
        return this.icons.filter(
          icon => icon.filename.toLowerCase().indexOf(this.search) !== -1
        );
      }
      return this.icons;
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
.card-text {
  cursor: copy;
}
</style>
