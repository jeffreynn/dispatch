<template>
  <ValidationObserver v-slot="{ invalid, validated }">
    <v-navigation-drawer v-model="showCreateEdit" app clipped right width="500">
      <template v-slot:prepend>
        <v-list-item two-line>
          <v-list-item-content>
            <v-list-item-title v-if="id" class="title"> Edit </v-list-item-title>
            <v-list-item-title v-else class="title"> New </v-list-item-title>
            <v-list-item-subtitle>Task</v-list-item-subtitle>
          </v-list-item-content>
          <v-btn
            icon
            color="info"
            :loading="loading"
            :disabled="invalid || !validated"
            @click="save()"
          >
            <v-icon>save</v-icon>
          </v-btn>
          <v-btn icon color="secondary" @click="closeCreateEdit()">
            <v-icon>close</v-icon>
          </v-btn>
        </v-list-item>
      </template>
      <v-card flat>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12>
                <ValidationProvider name="description" immediate>
                  <v-textarea
                    v-model="description"
                    slot-scope="{ errors, valid }"
                    label="Description"
                    :error-messages="errors"
                    :success="valid"
                    hint="The task's description."
                    clearable
                    required
                  />
                </ValidationProvider>
              </v-flex>
              <v-flex xs12>
                <project-select v-model="project" />
              </v-flex>
              <v-flex xs12>
                <v-select
                  v-model="status"
                  label="Status"
                  :items="statuses"
                  hint="The incident's current status"
                />
              </v-flex>
              <v-flex xs12>
                <ValidationProvider name="incident" rules="required" immediate>
                  <incident-select
                    v-model="incident"
                    slot-scope="{ errors, valid }"
                    label="Incident"
                    :error-messages="errors"
                    :success="valid"
                    hint="The tasks associated incident"
                    clearable
                    required
                  />
                </ValidationProvider>
              </v-flex>
              <v-flex xs12>
                <ValidationProvider name="owner" rules="required" immediate>
                  <participant-select
                    v-model="owner"
                    slot-scope="{ errors, valid }"
                    label="Owner"
                    :error-messages="errors"
                    :success="valid"
                    hint="The tasks current owner"
                    clearable
                    required
                  />
                </ValidationProvider>
              </v-flex>
              <v-flex xs12>
                <ValidationProvider name="assignees" rules="required" immediate>
                  <assignee-combobox
                    v-model="assignees"
                    slot-scope="{ errors, valid }"
                    label="Assignees"
                    :error-messages="errors"
                    :success="valid"
                    hint="The tasks current assignees"
                    clearable
                    required
                  />
                </ValidationProvider>
              </v-flex>
              <v-flex xs12>
                <span class="subtitle-2">Resolved At</span>
              </v-flex>
              <v-flex xs12>
                <v-row>
                  <v-col cols="6">
                    <date-picker-menu v-model="resolved_at" />
                  </v-col>
                  <v-col cols="6">
                    <time-picker-menu v-model="resolved_at" />
                  </v-col>
                </v-row>
              </v-flex>
              <v-flex xs12>
                <span class="subtitle-2">Resolve By</span>
              </v-flex>
              <v-flex xs12>
                <v-row>
                  <v-col cols="6">
                    <date-picker-menu v-model="resolve_by" />
                  </v-col>
                  <v-col cols="6">
                    <time-picker-menu v-model="resolve_by" />
                  </v-col>
                </v-row>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
      </v-card>
    </v-navigation-drawer>
  </ValidationObserver>
</template>

<script>
import { mapFields } from "vuex-map-fields"
import { mapActions } from "vuex"
import { ValidationObserver, ValidationProvider, extend } from "vee-validate"
import ProjectSelect from "@/project/ProjectSelect.vue"
import IncidentSelect from "@/incident/IncidentSelect.vue"
import ParticipantSelect from "@/incident/ParticipantSelect.vue"
import AssigneeCombobox from "@/task/AssigneeCombobox.vue"
import DatePickerMenu from "@/components/DatePickerMenu.vue"
import TimePickerMenu from "@/components/TimePickerMenu.vue"
import { required } from "vee-validate/dist/rules"

extend("required", {
  ...required,
  message: "This field is required",
})

export default {
  name: "TaskNewEditSheet",

  components: {
    ValidationObserver,
    ValidationProvider,
    IncidentSelect,
    AssigneeCombobox,
    ParticipantSelect,
    ProjectSelect,
    TimePickerMenu,
    DatePickerMenu,
  },

  data() {
    return {
      statuses: ["Open", "Resolved"],
    }
  },

  computed: {
    ...mapFields("task", [
      "selected.status",
      "selected.owner",
      "selected.assignees",
      "selected.description",
      "selected.creator",
      "selected.project",
      "selected.resolved_at",
      "selected.resolve_by",
      "selected.incident",
      "selected.id",
      "selected.loading",
      "dialogs.showCreateEdit",
    ]),
    ...mapFields("route", ["query"]),
  },

  methods: {
    ...mapActions("task", ["save", "closeCreateEdit"]),
  },

  created() {
    if (this.query.project) {
      this.project = { name: this.query.project }
    }
  },
}
</script>
