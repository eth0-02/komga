<template>
  <div>
    <v-dialog v-model="modal"
              max-width="450"
    >
      <v-card>
        <v-card-title>Delete Read List</v-card-title>

        <v-card-text>
          <v-container fluid>
            <v-row>
              <v-col>The read list <b>{{ readList.name }}</b> will be removed from this server. Your media files will
                not be affected. This <b>cannot</b> be undone. Continue ?
              </v-col>
            </v-row>

            <v-row>
              <v-col>
                <v-checkbox v-model="confirmDelete" color="red">
                  <template v-slot:label>
                    Yes, delete the read list "{{ readList.name }}"
                  </template>
                </v-checkbox>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer/>
          <v-btn text @click="dialogCancel">Cancel</v-btn>
          <v-btn text class="red--text"
                 @click="dialogConfirm"
                 :disabled="!confirmDelete"
          >Delete
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-snackbar
      v-model="snackbar"
      bottom
      color="error"
    >
      {{ snackText }}
      <v-btn
        text
        @click="snackbar = false"
      >
        Close
      </v-btn>
    </v-snackbar>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'ReadListDeleteDialog',
  data: () => {
    return {
      confirmDelete: false,
      snackbar: false,
      snackText: '',
      modal: false,
    }
  },
  props: {
    value: Boolean,
    readList: {
      type: Object as () => ReadListDto,
      required: true,
    },
  },
  watch: {
    value (val) {
      this.modal = val
    },
    modal (val) {
      !val && this.dialogCancel()
    },
  },
  methods: {
    dialogCancel () {
      this.$emit('input', false)
      this.confirmDelete = false
    },
    dialogConfirm () {
      this.delete()
      this.$emit('input', false)
    },
    showSnack (message: string) {
      this.snackText = message
      this.snackbar = true
    },
    async delete () {
      try {
        await this.$komgaReadLists.deleteReadList(this.readList.id)
        this.$emit('deleted', true)
      } catch (e) {
        this.showSnack(e.message)
      }
    },
  },
})
</script>

<style scoped>

</style>
