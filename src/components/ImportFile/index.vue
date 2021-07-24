<template>
  <v-container>
    <v-alert
      v-if="alert"
      border="right"
      color="red"
      elevation="24"
      type="error"
      >{{ error }}</v-alert
    >
    <div class="pa-2">
      <v-row justify-md="center">
        <v-col cols="12">
          <h2>INSIRA ARQUIVO DOS TIPOS: XLS OU XLSX</h2>
        </v-col>
        <v-col cols="12">
          <v-file-input
            @change="handle($event)"
            counter
            label="Insira o arquivo"
            prepend-icon="mdi-paperclip"
            outlined
            :show-size="1000"
            accept=".xls,.xlsx "
          >
            <template v-slot:selection="{ index, text }">
              <v-chip v-if="index < 2" dark label small>
                {{ text }}
              </v-chip>
            </template>
          </v-file-input>
        </v-col>
        <v-col cols="12">
          <v-btn
            block
            color="blue"
            large
            :loading="loading"
            x-large
            @click="importar()"
            >Importar</v-btn
          >
        </v-col>
      </v-row>
      <v-row>
        {{ array }}
      </v-row>
    </div>
  </v-container>
</template>
<script>
import XLSX from "xlsx";
export default {
  data: () => ({
    alert: false,
    error: null,
    data: [],
    type: null,
    array: null,
    loading: false,
    anexo: true,
  }),
  watch: {
    alert() {
      setTimeout(() => {
        this.alert = false;
        this.error = null;
      }, 3000);
    },
  },
  methods: {
    async handle(e) {
      try {
        if (e) {
          if (
            e.type !==
            "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
          ) {
            throw new Error("Tipo de arquivo invalido");
          }
          const file = e;
          const reader = new FileReader();
          reader.onload = (e) => {
            const bstr = e.target.result;
            const wb = XLSX.read(bstr, { type: "binary" });
            const wsname = wb.SheetNames[0];
            const ws = wb.Sheets[wsname];
            const data = XLSX.utils.sheet_to_json(ws, { header: 1 });
            this.data = data;
          };
          reader.readAsBinaryString(file);
        }
      } catch (error) {
        this.alert = true;
        this.error = error.message;
      }
    },
    async importar() {
      this.array = [];
      this.loading = true;
      if (this.data.length) {
        var dados = this.data;
        var headers = dados[0];
        dados.forEach((element, index) => {
          if (index != 0) {
            var obj = {};
            for (var i = 0; i < headers.length; i++) {
              var novo = { [headers[i]]: element[i] };
              obj = { ...obj, ...novo };
            }
            if (obj[headers[0]] != undefined) {
              this.array.push(obj);
            }
          }
        });
      }
      this.loading = false;
    },
  },
};
</script>
