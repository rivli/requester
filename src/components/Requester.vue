<template>
  <div class="appwrapper" :class="{ darktheme: settings.darktheme }">
    <div class="container">
      <div class="header">
        <div class="title">Requester</div>
        <settings v-model="settings" />
      </div>

      <div class="body">
        <div class="workplace">
          <div class="title">Parameters</div>

          <div>
            <div class="main_set">
              <span v-if="settings.host.length" class="server_url">{{
                settings.host
              }}</span>
              <input
                v-model="url"
                type="text"
                :class="{ fullBorder: !settings.host.length }"
                :placeholder="
                  settings.host.length
                    ? 'research/get/ - for example'
                    : 'http://my-site.com/api/research/get/ - for example'
                "
              />
              <select v-model="request_type">
                <option value="POST">POST</option>
                <option value="GET">GET</option>
              </select>
              <button class="go_btn" @click="makeRequest">Go</button>
            </div>
            <br />

            <div class="fields">
              <div class="fields_header">
                <div
                  class="title"
                  :class="{ active: active_view === 'fields' }"
                  @click="active_view = 'fields'"
                >
                  Fields ({{ fields.length }})
                </div>
                <div
                  class="title"
                  :class="{ active: active_view === 'headers' }"
                  @click="active_view = 'headers'"
                >
                  Headers ({{ headers.length }})
                </div>
              </div>

              <template v-if="active_view === 'fields'">
                <table>
                  <tbody>
                    <tr>
                      <td colspan="4">
                        <div v-if="json_error" class="error_msg">
                          wrong JSON
                        </div>
                        <textarea
                          class="fields_json"
                          placeholder='You can add fields as json. Example - {"result":true, "count":42}'
                          v-model="json_fields"
                        />
                      </td>
                    </tr>
                    <tr v-for="(field, key) in fields" :key="`field${key}`">
                      <td>
                        <select v-model="field.type">
                          <option value="text">text</option>
                          <option value="number">number</option>
                          <option v-if="request_type === 'POST'" value="file">
                            file
                          </option>
                          <option v-if="request_type === 'POST'" value="object">
                            object
                          </option>
                        </select>
                      </td>
                      <td>
                        <input
                          type="text"
                          v-model="field.name"
                          placeholder="Field name"
                        />
                      </td>
                      <td>
                        <input
                          v-if="field.type !== 'file'"
                          :type="field.type"
                          v-model="field.value"
                          :placeholder="field.type"
                        />
                        <input
                          v-else-if="field.type === 'file'"
                          type="file"
                          @change="(e) => uploadFile(e, field)"
                          :placeholder="field.type"
                        />
                      </td>
                      <td>
                        <div class="action_icon_bloc" @click="deleteField(key)">
                          <iconics icon="cancel" />
                        </div>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <div class="action_icon_bloc" @click="addField">
                          <template v-if="!fields.length">Add field</template>
                          <iconics icon="add" />
                        </div>
                      </td>
                      <td colspan="3" />
                    </tr>
                  </tbody>
                </table>
              </template>

              <template v-else>
                <table>
                  <tbody>
                    <tr v-for="(header, key) in headers" :key="`header${key}`">
                      <td>
                        <input
                          type="text"
                          v-model="header.key"
                          placeholder="Key"
                        />
                      </td>
                      <td>
                        <input
                          type="text"
                          v-model="header.value"
                          placeholder="Value"
                        />
                      </td>
                      <td>
                        <div
                          class="action_icon_bloc"
                          @click="deleteHeader(key)"
                        >
                          <iconics icon="cancel" />
                        </div>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <div class="action_icon_bloc" @click="addHeader">
                          <template v-if="!headers.length">Add header</template>
                          <iconics icon="add" />
                        </div>
                      </td>
                      <td colspan="2" />
                    </tr>
                  </tbody>
                </table>
              </template>
            </div>
          </div>
        </div>
        <br />

        <div v-if="this.show_response">
          <div class="title">
            {{
              response
                ? "Response"
                : errors.response || errors.request || errors.other
                ? "Error"
                : loading
                ? "Loading"
                : "Ooops, idk what happened..."
            }}
          </div>
          <div>
            <b>URL:</b> {{ request_info.url }} <br />
            <div v-if="response" class="answer">
              <pre>{{ response }}</pre>
            </div>
            <div v-if="errors.response">
              <span class="text-subtitle-2">
                The request was made and the server responded with a status code
                that falls out of the range of 2xx
              </span>
              <div class="answer">{{ errors.response }}</div>
            </div>
            <div v-if="errors.request">
              <span class="text-subtitle-2">
                The request was made but no response was received
                `error.request` is an instance of XMLHttpRequest in the browser
                and an instance of http.ClientRequest in node.js
              </span>
              <div class="answer">{{ errors.request }}</div>
            </div>
            <div v-if="errors.other">
              <span class="text-subtitle-2">
                Something happened in setting up the request that triggered an
                Error
              </span>
              <div class="answer">{{ errors.other }}</div>
            </div>
            <iconics v-if="loading" icon="loading" size="24px" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Settings from "./Settings.vue";
import Iconics from "./Iconics.vue";
export default {
  components: { Settings, Iconics },
  name: "PageRequester",
  layout: "dashboard",
  head: {
    title: "Requester",
  },

  data: () => ({
    url: "",
    request_type: "GET",
    request_info: {
      url: false,
    },

    active_view: "fields",
    headers: [],
    fields: [],
    json_fields: "",
    json_error: false,
    show_response: false,
    response: false,
    loading: false,
    errors: {
      response: false,
      request: false,
      other: false,
    },

    user: {
      token: false,
    },

    settings: {
      host: "",
      darktheme: false,
    },
  }),

  computed: {
    //
  },

  async mounted() {
    if (localStorage.getItem("requester")) {
      const requesterSettings = JSON.parse(localStorage.getItem("requester"));

      this.settings = requesterSettings;
    }
  },

  methods: {
    addField() {
      this.fields.push({
        type: "text",
        name: "",
        value: "",
      });
    },

    deleteField(index) {
      this.fields.splice(index, 1);
    },

    addHeader() {
      this.headers.push({
        key: "",
        value: "",
      });
    },

    deleteHeader(index) {
      this.headers.splice(index, 1);
    },

    async parseJSONFields(json, fields) {
      try {
        const jsonFields = await JSON.parse(json);

        Object.keys(jsonFields).map((key) => {
          const type =
            typeof jsonFields[key] === "string"
              ? "text"
              : typeof jsonFields[key] === "number"
              ? "number"
              : "object";
          fields.push({
            type,
            name: key,
            value: jsonFields[key],
          });
        });

        json = "";
        this.json_fields = "";
      } catch (error) {
        this.json_error = true;
      }
    },

    uploadFile(e, field) {
      field.value = e.target.files[0];
    },

    handleError(error) {
      if (error.response) {
        // The request was made and the server responded with a status code
        // that falls out of the range of 2xx
        this.errors.response = error.response;
        console.log(error.response);
      } else if (error.request) {
        // The request was made but no response was received
        // `error.request` is an instance of XMLHttpRequest in the browser and an instance of
        // http.ClientRequest in node.js
        this.errors.request = error.request;
      } else {
        // Something happened in setting up the request that triggered an Error
        this.errors.other = error.message;
      }
    },

    async makeRequest() {
      this.show_response = true;
      this.response = false;
      this.errors = {
        response: false,
        request: false,
        other: false,
      };
      this.loading = true;
      let config = {
        headers: {},
      };

      this.headers.map((h) => {
        config.headers[h.key] = h.value;
      });

      if (this.request_type === "GET") {
        let params = "?";
        if (this.json_fields !== "")
          this.parseJSONFields(this.json_fields, this.fields);
        for (const param of this.fields) {
          params += `${param.name}=${param.value}&`;
        }

        params = params.slice(0, -1);
        this.request_info.url = this.settings.host.length
          ? this.settings.host
          : "";
        this.request_info.url += this.url + params;
        try {
          const rr = await axios.get(this.request_info.url, config);
          this.response = rr.data;
        } catch (error) {
          this.handleError(error);
        }
      } else {
        const formData = new FormData();
        if (this.json_fields !== "")
          this.parseJSONFields(this.json_fields, this.fields);
        let fileCounter = 0;
        for (const param of this.fields) {
          if (param.type === "file") {
            config.headers["Content-Type"] = "multipart/form-data";
            if (!param.name.length) {
              param.name = fileCounter === 0 ? "file" : "file_" + fileCounter;
              fileCounter++;
            }
            formData.append(param.name, param.value);
          } else {
            formData.append(param.name, param.value);
          }
        }

        this.request_info.url = this.settings.host.length
          ? this.settings.host
          : "";
        this.request_info.url += this.url;
        try {
          const rr = await axios.post(this.request_info.url, formData, config);
          this.response = rr.data;
        } catch (error) {
          this.handleError(error);
        }
      }
      this.loading = false;
    },
  },
};
</script>

<style lang="scss" scoped>
.action_icon_bloc {
  display: flex;
  align-items: center;

  svg {
    cursor: pointer;
  }
}

.fields {
  table {
    width: 100%;
    td {
      text-align: center;
      input,
      select {
        width: 100%;
      }
    }
  }
}

.fields_header {
  display: flex;
  margin-bottom: 10px;
  border-bottom: 1px solid orange;
  padding: 0 20px;

  .title {
    cursor: pointer;
    border: 0;
    margin-bottom: -1px;
    padding: 5px 8px;

    &.active {
      color: #333;
      background: orange;
    }

    &:hover {
      opacity: 0.8;
    }
  }
}
</style>
