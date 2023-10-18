<template>
  <div class="schema">
    <!-- Objects -->
    <div
        v-if="schema.type === 'object'"
        class="schema-field schema-field-object"
        :class="{'schema-field--root': root}"
    >
      <div class="schema-field--info">
        <p
            v-if="hasName"
            class="schema-field--info--name"
        ><strong>{{ name }}</strong></p>
        <p v-if="hasName" class="schema-field--info--type">{{ schema.type }}</p>
        <p
            v-if="required"
            class="schema-field--info--required text-primary-500"
        >required</p>
      </div>

      <div v-if="schema.description" v-html="schema.description"></div>

      <button class="schema-field--button" v-if="hasName" @click="toggleShowObject">
        <p v-if="!showObject">Show attributes</p>
        <p v-else>Hide attributes</p>
      </button>
      <div
          v-if="showObject || !hasName"
          :class="{ 'schema-field--collapse': hasName}"
      >
        <div
            v-for="(property, propName) in schema.properties"
            :key="propName"
        >
          <SchemaRenderer
              :schema="property"
              :name="propName"
              :required="isFieldRequired(schema, propName)"
          />
        </div>
      </div>
    </div>

    <!-- Arrays -->
    <div
        v-else-if="schema.type === 'array'"
        class="schema-field schema-field--array"
    >
      <SchemaRenderer :schema="schema.items" :name="name"  />
    </div>

    <!-- Primitives -->
    <div
        v-else-if="['string', 'number', 'boolean', 'integer'].includes(schema.type)"
        class="schema-field"
    >
      <div class="schema-field--info">
        <p
            v-if="hasName"
            class="schema-field--info--name"
        ><strong>{{ name }}</strong></p>
        <p class="schema-field--info--type">{{ schema.type }}</p>
        <p
            v-if="required"
            class="schema-field--info--required text-primary-500"
        >required</p>
      </div>

      <div v-if="schema.description" v-html="schema.description"></div>

      <div v-if="schema.maxLength">
        Max length: {{ schema.maxLength }}
      </div>

      <div v-if="schema.minLength">
        Min length: {{ schema.minLength }}
      </div>

      <div v-if="schema.enum">
        Allowed values: {{ schema.enum.join(', ') }}
      </div>

      <div v-if="schema.default">
        Default: {{ schema.default }}
      </div>

      <div v-if="schema.format">
        Format: {{ schema.format }}
      </div>

      <div v-if="schema.example">
        Example: {{ schema.example }}
      </div>
    </div>

  </div>
</template>
  
<script>
export default {
  props: {
    root: {
      type: Boolean,
      default: false
    },
    schema: {
      type: Object,
      required: true
    },
    name: {
      type: String,
      required: false,
      default: "",
    },
    required: {
      type: Boolean,
      required: false,
      default: false,
    }
  },
  components: {
    SchemaRenderer: () => import('./schema.vue') // Recursive component
  },
  data() {
    return {
      showObject: false
    }
  },
  computed: {
    hasName() {
      return this.name !== ""
    }
  },
  methods: {
    toggleShowObject() {
      this.showObject = !this.showObject
    },
    isFieldRequired(schema, property) {
      const hasSchema = schema?.required
      if (hasSchema === undefined) {
        return  false
      }

      return schema.required.includes(property)
    }
  }
}
</script>

<style scoped>
.schema-field {
  padding: 1rem 0rem;
  border-bottom-width: 1px;
  border-bottom-color: #edf2f7;
}

p {
  margin: 0 0;
}

.schema-field--info {
  display: flex;
  align-items: baseline;
  margin-bottom: 0.5rem;
}

.schema-field--info--required {
  text-transform: uppercase;
  letter-spacing: 1px;
  font-size: 0.8rem;
  font-weight: 800;
}

.schema-field--info p {
  margin: 0 10px 0 0;
}

.schema-field--array {
  border-bottom: 0;
  padding: 0;
}

.schema-field--collapse {
  padding: 0 1rem;
  margin-top: 1rem;
  border-radius: 8px;
  border: 1px solid #edf2f7;
}

.schema-field--button {
  color: #0b68e3;
}

.schema-field--root {
  border: 0;
}
</style>