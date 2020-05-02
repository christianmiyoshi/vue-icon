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

        <v-row justify="center">
          <v-col v-for="icon in icons" v-bind:key="icon.id" :cols="2">
            <v-card class="mx-auto" max-width="400">
              <v-img
                class="white--text align-end"
                height="200px"
                :src="icon.url"
                contain
                aspect-ratio="1"
              >
              </v-img>
              <v-card-subtitle>
                {{ icon.filename }}
              </v-card-subtitle>
            </v-card></v-col
          >
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import Vue from "vue";

interface IconDataIntarface {
  id: number;
  filename: string;
  size: number;
  url: string;
}

export default Vue.extend({
  name: "HelloWorld",

  data: () => ({
    icons: [] as IconDataIntarface[]
  }),
  methods: {
    onFileChange: function(event: Event) {
      const target = event.target as HTMLInputElement;
      const files: IconDataIntarface[] = Array.from(target.files || []).map(
        file => ({
          id: Math.random(),
          filename: file.name,
          size: file.size,
          url: URL.createObjectURL(file)
        })
      );
      this.icons.push(...files);
    }
  }
});
</script>
