<template>
  <v-dialog
    :model-value="modelValue"
    @update:model-value="$emit('update:modelValue', $event)"
    max-width="800px"
    persistent
  >
    <v-card>
      <v-card-title class="text-h5">
        Workflow Details
      </v-card-title>
      
      <v-card-text>
        <v-container>
          <v-row>
            <v-col cols="12">
              <h3>Transaction Information</h3>
              <p><strong>Transaction ID:</strong> {{ menthumCore.txn_id_internal || 'N/A' }}</p>
              <p><strong>Status:</strong> {{ menthumCore.status || 'N/A' }}</p>
              <p><strong>Amount:</strong> {{ menthumCore.amount || 'N/A' }}</p>
              <p><strong>Payment Type:</strong> {{ menthumCore.payment_type || 'N/A' }}</p>
            </v-col>
          </v-row>
          
          <!-- Add your workflow content here -->
          <v-row>
            <v-col cols="12">
              <v-card variant="outlined" class="pa-4">
                <h4>Workflow Steps</h4>
                <v-list>
                  <v-list-item>
                    <v-list-item-title>Step 1: Initiated</v-list-item-title>
                    <v-list-item-subtitle>{{ menthumCore.created_on || 'N/A' }}</v-list-item-subtitle>
                  </v-list-item>
                  <v-list-item>
                    <v-list-item-title>Step 2: Processing</v-list-item-title>
                    <v-list-item-subtitle>In Progress...</v-list-item-subtitle>
                  </v-list-item>
                  <v-list-item>
                    <v-list-item-title>Step 3: Completed</v-list-item-title>
                    <v-list-item-subtitle>{{ menthumCore.updated_on || 'Pending' }}</v-list-item-subtitle>
                  </v-list-item>
                </v-list>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>
      
      <v-card-actions>
        <v-spacer />
        <v-btn
          color="grey-darken-1"
          variant="text"
          @click="closeDialog"
        >
          Close
        </v-btn>
        <v-btn
          color="blue-darken-1"
          variant="text"
          @click="saveWorkflow"
        >
          Save
        </v-btn>
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
  methods: {
    closeDialog() {
      this.$emit('update:modelValue', false);
    },
    saveWorkflow() {
      // Add your save logic here
      console.log('Saving workflow for:', this.menthumCore);
      this.$emit('update:modelValue', false);
    }
  }
};
</script>

<style scoped>
/* Add any custom styles here */
</style>