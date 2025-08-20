<template>
  <v-dialog
    :model-value="modelValue"
    @update:model-value="$emit('update:modelValue', $event)"
    max-width="500px"
  >
    <v-card>
      <v-card-title>
        TEST DIALOG - IT WORKS!
      </v-card-title>
      <v-card-text>
        <p>This is a test dialog to verify the v-model binding works.</p>
        <p>modelValue: {{ modelValue }}</p>
        <p>menthumCore: {{ JSON.stringify(menthumCore, null, 2) }}</p>
      </v-card-text>
      <v-card-actions>
        <v-spacer />
        <v-btn @click="$emit('update:modelValue', false)">Close</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  name: "WorkflowDialog",
  props: {
    modelValue: {
      type: Boolean,
      default: false
    },
    menthumCore: {
      type: Object,
      default: () => ({})
    }
  },
  emits: ['update:modelValue'],
  mounted() {
    console.log('WorkflowDialog mounted with modelValue:', this.modelValue);
  },
  watch: {
    modelValue(newVal) {
      console.log('WorkflowDialog modelValue changed to:', newVal);
    }
  }
};
</script>