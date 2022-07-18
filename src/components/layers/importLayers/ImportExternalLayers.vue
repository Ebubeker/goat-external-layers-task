<template>
  <div class="text-center">
    <v-dialog v-model="dialog" width="700">
      <template v-slot:activator="{ on, attrs }">
        <v-btn color="success" dark v-bind="attrs" v-on="on">
          Import External Layer
        </v-btn>
      </template>

      <v-card style="padding: 40px;">
        <v-card-text class="pa-0">
          <h1 class="mb-4">Import a Layer</h1>
          <v-alert v-if="error" type="error">{{ error }}</v-alert>
          <v-form ref="form" lazy-validation>
            <v-text-field
              v-model="url"
              label="Source Url"
              required
            ></v-text-field>
          </v-form>
          <div>
            <p class="h6">Results</p>
            <v-card
              v-for="(simpleLayer, idx) in layerListToAdd"
              :key="idx"
              class="mx-auto mb-2"
              style="display: flex"
            >
              <v-card-text>
                <div>{{ simpleLayer.title }}</div>
                <p>{{ simpleLayer.url }}</p>
              </v-card-text>
              <v-card-actions>
                <v-btn
                  text
                  @click="$emit('getLayerInfo', simpleLayer)"
                  color="deep-purple accent-4"
                >
                  <v-icon small color="success">fas fa-circle-plus</v-icon>
                </v-btn>
              </v-card-actions>
            </v-card>
          </div>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="success" text @click="layerDataHandler">
            Import Layers
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  data: () => ({
    url: "",
    dialog: false,
    error: "",
    layerListToAdd: []
  }),
  methods: {
    layerDataHandler() {
      if (this.url !== "") {
        this.error = "";
        const data = {
          layer_url: this.url
        };
        this.findAllAvailableLayers(data);
        // this.dialog = false;
        this.url = "";
      } else {
        this.error = "Make sure to fill all the required fields";
      }
    },
    findAllAvailableLayers(data) {
      fetch(data.layer_url)
        .then(result => result.text())
        .then(datares => {
          let parser = new DOMParser(),
            xmlDoc = parser.parseFromString(datares, "text/xml");
          let names = [...xmlDoc.getElementsByTagName("Layer")];
          names.forEach((layerElement, idx) => {
            if (idx !== 0) {
              let layerPossibility = {
                title: layerElement.getElementsByTagName("Title")[0]
                  .textContent,
                name: layerElement.getElementsByTagName("Name")[0].textContent,
                url: data.layer_url.split("?")[0] + "?"
              };
              this.layerListToAdd.push(layerPossibility);
            }
          });
        });
    }
  }
};
</script>

<style></style>
