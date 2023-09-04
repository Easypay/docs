<template>
  <div>
    <p v-html="openApiSpec.info.description"></p>

    <div v-for="pathKey in Object.keys(openApiSpec.paths)" :key="pathKey">
      <div v-for="methodKey in Object.keys(openApiSpec.paths[pathKey])" :key="methodKey">

        <h1>{{ openApiSpec.paths[pathKey][methodKey].summary }}</h1>
        <p><strong>{{ methodKey.toUpperCase() }}</strong> {{ pathKey }}</p>
        <div v-html="openApiSpec.paths[pathKey][methodKey].description"></div>

        <!-- Path Parameters -->
        <div v-if="openApiSpec.paths[pathKey].parameters">
          <h3>Path parameters:</h3>

          <div v-for="param in openApiSpec.paths[pathKey].parameters" :key="param.name">
            <SchemaRenderer v-if="param.schema !== undefined" :name="param.name" :schema="param.schema" :required="param.required" />
          </div>
        </div>

        <div v-if="openApiSpec.paths[pathKey][methodKey].parameters">
          <h3>Request parameters:</h3>

          <div v-for="param in getParametersFor(openApiSpec.paths[pathKey][methodKey].parameters, 'query')" :key="param.name">
            <SchemaRenderer
                v-if="param.schema.type !== 'array'"
                :name="param.name"
                :schema="param.schema"
                :root="true"
            />
          </div>
        </div>

        <!-- Request Body -->
        <div v-if="openApiSpec.paths[pathKey][methodKey].requestBody">
          <h3>Request Body</h3>
          <SchemaRenderer
              :root="true"
              :schema="openApiSpec.paths[pathKey][methodKey].requestBody.content['application/json'].schema" />
        </div>

        <!-- Responses -->
        <div v-if="openApiSpec.paths[pathKey][methodKey].responses">
          <h3>Responses:</h3>
          <div v-for="responseKey in Object.keys(openApiSpec.paths[pathKey][methodKey].responses)" :key="responseKey">
            <h5>Status: {{ responseKey }}</h5>
            <p>{{ openApiSpec.paths[pathKey][methodKey].responses[responseKey].description }}</p>
            <div v-if="'content' in openApiSpec.paths[pathKey][methodKey].responses[responseKey]">
              <SchemaRenderer
                  :root="true"
                  :schema="openApiSpec.paths[pathKey][methodKey].responses[responseKey].content['application/json'].schema"
              />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import $RefParser from 'json-schema-ref-parser';
import SchemaRenderer from "./schema"

export default {
  components: {
    SchemaRenderer,
  },
  data() {
    return {
      openApiSpec: null
    };
  },
  methods: {
    getParametersFor(params, on) {
      return params.filter((param) => param.in === on)
    }
  },
  async fetch() {
    const rawSpec = await this.$content('paybylink').fetch();
    this.openApiSpec = await $RefParser.dereference(rawSpec);
  },
  fetchOnServer: true,
}
</script>
