<template>
  <div id="contact-info">
    <!-- SUMMARY SECTION -->
    <template v-if="!isEditing">
      <!-- Display Contact Info -->
      <v-row no-gutters class="edit-section">
        <v-col cols="12" sm="3" class="pr-4">
          <label class="contact-info-label">
            <span>{{ contactLabel }} Contact Information</span>
            <v-flex>
              <v-chip v-if="hasBusinessContactInfoChange" x-small label color="primary" text-color="white">
                {{editedLabel}}
              </v-chip>
            </v-flex>
          </label>
        </v-col>

        <!-- Email Address -->
        <v-col cols="12" sm="4" class="pr-4">
          <label class="item-label">Email Address</label>
          <div id="lbl-email">
            {{ !!businessContact.email ? businessContact.email : "(Not entered)" }}
          </div>
        </v-col>

        <!-- Phone Number -->
        <v-col cols="6" sm="3" class="pr-4">
          <label class="item-label">Phone Number</label>
          <div v-if="!!businessContact.phone" id="lbl-phone">
            {{ businessContact.phone }}
            <span v-if="!!businessContact.extension">Ext: {{ businessContact.extension }}</span>
          </div>
          <div v-else id="lbl-no-phone">(Not entered)</div>
        </v-col>

        <!-- Edit Actions -->
        <v-col v-if="!disableActions" cols="6" sm="2">
          <div class="d-flex justify-end align-end align-sm-start">
            <v-btn
              v-if="hasBusinessContactInfoChange"
              id="contact-info-undo-btn"
              text color="primary"
              @click="resetContactInfo()"
            >
              <v-icon small>mdi-undo</v-icon>
              <span>Undo</span>
            </v-btn>

            <v-tooltip
              v-else top
              content-class="top-tooltip"
              transition="fade-transition"
              nudge-right="3"
              :disabled="disableActionTooltip"
            >
              <template v-slot:activator="{ on }">
                <v-btn
                  v-on="on"
                  id="contact-info-edit-btn"
                  text color="primary"
                  @click="isEditing = true"
                >
                  <v-icon small>mdi-pencil</v-icon>
                  <span>{{editLabel}}</span>
                </v-btn>
              </template>
              <span>No fee to change</span>
            </v-tooltip>

            <!-- Drop Down Actions -->
            <span class="more-actions" v-if="hasBusinessContactInfoChange">
              <v-menu
                offset-y left nudge-bottom="4"
                v-model="dropdown"
              >
                <template v-slot:activator="{ on }">
                  <v-btn
                    text small color="primary"
                    id="btn-more-actions"
                    v-on="on"
                  >
                    <v-icon>{{dropdown ? 'mdi-menu-up' : 'mdi-menu-down'}}</v-icon>
                  </v-btn>
                </template>
                <v-list>
                  <v-list-item
                    id="btn-more-actions-edit"
                    class="v-list-item"
                    @click="isEditing = true; dropdown = false"
                  >
                    <v-list-item-subtitle>
                      <v-icon small color="primary">mdi-pencil</v-icon>
                      <span class="drop-down-action ml-1">Change</span>
                    </v-list-item-subtitle>
                  </v-list-item>
                </v-list>
              </v-menu>
            </span>
          </div>
        </v-col>
      </v-row>
    </template>

    <!-- EDIT SECTION -->
    <template v-else>
      <v-row no-gutters class="summary-section">
        <v-col cols="12" sm="3">
          <label class="title-label" :class="{'error-text': invalidSection}">
            {{ contactLabel }} Contact Information
          </label>
        </v-col>

        <v-col v-if="customMsg" cols="12" sm="9">
          {{ customMsg }}
        </v-col>

        <v-col v-else cols="12" sm="9">
          There is no fee or filing to change {{ contactLabel }} Contact Information. Any
          changes made will be applied immediately.
        </v-col>
      </v-row>

      <v-form
        v-model="formValid"
        ref="editContactForm"
        name="business-contact-form"
        class="business-contact-form pt-5"
        @submit.prevent="submitContact()"
      >
        <!-- Email Address -->
        <v-row no-gutters>
          <v-col cols="12" sm="3" class="pr-4">
            <label class="title-label">Email Address</label>
          </v-col>

          <v-col cols="12" sm="9">
            <v-text-field
              id="txt-email"
              filled
              label="Email Address"
              req
              persistent-hint
              :rules="emailRules"
              v-model="contactInfo.email"
              validate-on-blur
            >
            </v-text-field>
          </v-col>
        </v-row>

        <!-- Confirm Email -->
        <v-row no-gutters>
          <v-col cols="12" sm="3" class="pr-4">
            <label class="title-label">Confirm Email</label>
          </v-col>

          <v-col cols="12" sm="9">
            <v-text-field
              id="txt-confirm-email"
              filled
              label="Confirm Email Address"
              req
              persistent-hint
              :rules="confirmEmailRules"
              v-model="contactInfo.confirmEmail"
              validate-on-blur
            >
            </v-text-field>
          </v-col>
        </v-row>

        <!-- Phone Number -->
        <v-row no-gutters>
          <v-col cols="12" sm="3" class="pr-4">
            <label class="title-label">Phone Number</label>
          </v-col>

          <v-col cols="6" sm="5" class="pr-4">
            <v-text-field
              id="txt-phone"
              filled
              :label="phoneLabel"
              persistent-hint
              hint="Example: (555) 555-5555"
              type="tel"
              v-mask="['(###) ###-####']"
              v-model="contactInfo.phone"
              :rules="phoneRules"
              validate-on-blur
            >
            </v-text-field>
          </v-col>

          <v-col cols="6" sm="4">
            <v-text-field
              id="txt-phone-extension"
              filled
              label="Extension (Optional)"
              persistent-hint
              v-mask="'#####'"
              v-model="contactInfo.extension"
            >
            </v-text-field>
          </v-col>
        </v-row>

        <!-- Form Actions -->
        <div class="action-btns d-flex justify-center justify-sm-end">
          <v-btn id="contact-info-done-btn" large color="primary" type="submit" value="Submit">
            <span>Save</span>
          </v-btn>
          <v-btn id="contact-info-cancel-btn" large outlined color="primary" @click="cancelEdit()">
            <span>Cancel</span>
          </v-btn>
        </div>
      </v-form>
    </template>
  </div>
</template>

<script lang="ts">
// Libraries
import { Component, Prop, Watch, Emit, Vue } from 'vue-property-decorator'
import { mask } from 'vue-the-mask'

// Interfaces & enums
import { ContactPointIF, FormIF } from '@bcrs-shared-components/interfaces'

@Component({
  directives: { mask }
})
export default class ContactInfo extends Vue {
  // Refs
  $refs!: Vue['$refs'] & {
    editContactForm: FormIF,
  }

  /** The current business contact information. */
  @Prop()
  readonly businessContact!: ContactPointIF

  /** The baseline contact information. */
  @Prop()
  readonly originalBusinessContact!: ContactPointIF

  /** Flag for identifying changes. */
  @Prop()
  readonly hasBusinessContactInfoChange!: boolean

  /** Contact information label. */
  @Prop({ default: 'Registered Office' })
  readonly contactLabel!: string

  /** Custom contact info msg. */
  @Prop({ default: null })
  readonly customMsg!: string

  /** Edit label name (ie 'Change' or 'Correct'). */
  @Prop()
  readonly editLabel!: string

  /** Edited label name (ie 'Changed' or 'Corrected'). */
  @Prop()
  readonly editedLabel!: string

  /** Option to disable the edit actions. */
  @Prop({ default: false })
  readonly disableActions!: boolean

  /** Option to disable the action tooltip. */
  @Prop({ default: false })
  readonly disableActionTooltip!: boolean

  /** Prompt error handling. */
  @Prop({ default: false })
  readonly invalidSection!: boolean

  /** Option to disable phone requirement. */
  @Prop({ default: false })
  readonly optionalPhone!: boolean

  // Local Properties
  private isEditing: boolean = false
  private contactInfo: ContactPointIF = null
  private formValid: boolean = false

  // Text-field Rules
  private emailRules = []
  private confirmEmailRules = []
  private phoneRules = []

  /** V-model for dropdown menu. */
  private dropdown: boolean = null

  /** The phone number text-field label. */
  private get phoneLabel (): string {
    let label = 'Phone Number '
    if (this.optionalPhone) label += '(Optional)'
    return label
  }

  /** Emit the updated data to parent component */
  private updateContactInfo (): void {
    this.emitContactInfo(this.contactInfo)
    this.isEditing = false
  }

  /** Cancel the current local edits */
  private cancelEdit (): void {
    this.contactInfo = { ...this.businessContact }
    this.emitContactInfo(this.contactInfo)
    this.isEditing = false
  }

  /** Restore the contact info to original values */
  private resetContactInfo (): void {
    this.contactInfo = { ...this.originalBusinessContact }
    this.emitContactInfo(this.contactInfo)
    this.isEditing = false
  }

  /** Submit method to handle validations and data update. */
  private async submitContact (): Promise<void> {
    // Set Rules on submission
    this.emailRules = [
      (v: string) => !!v || 'Email address is required',
      (v: string) => {
        // eslint-disable-next-line max-len
        const pattern = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
        return pattern.test(v) || 'Valid email is required'
      }
    ]
    this.confirmEmailRules = [
      (v: string) => !!v || 'Confirm email address',
      (v: string) => !v || (v.toString() === (this.$refs.editContactForm && this.$refs.editContactForm.$el[0].value)) ||
        'Email addresses must match'
    ]
    this.phoneRules = this.optionalPhone ? []
      : [
        (v: string) => !!v || 'Phone number is required',
        (v: any) => !v || (v.length === 0 || v.length === 14) || 'Phone number is invalid'
      ]

    // Validate form and wait for v-model to get updated
    this.$refs.editContactForm.validate()
    await Vue.nextTick()

    // When valid, submit data
    if (this.formValid) this.updateContactInfo()
  }

  /** Initialize the contact info. */
  @Watch('businessContact', { deep: true, immediate: true })
  private initializeContactInfo () {
    this.contactInfo = { ...this.businessContact, confirmEmail: this.businessContact.email }
  }

  /** Inform the parent of the current edit state. */
  @Watch('isEditing', { immediate: true })
  @Emit('isEditingContact')
  private emitIsEditing (isEditing: boolean): void { }

  @Emit('contactInfoChange')
  private emitContactInfo (contactInfo: ContactPointIF): void { }
}
</script>

<style lang="scss" scoped>
@import '@/assets/styles/theme.scss';

.edit-section {
  font-size: $px-16;
  color: $gray7;
}

.contact-info-label,
.title-label {
  font-weight: bold;
  color: $gray9;
}

.item-label {
  font-size: $px-14;
  font-weight: bold;
}

.action-btns {
  .v-btn + .v-btn {
    margin-left: 0.5rem;
  }

  .v-btn {
    min-width: 6.5rem;
  }
}

#contact-info-undo-btn {
  border-right: 1px solid $gray1;
}

#contact-info-done-btn[disabled] {
  color: white !important;
  background-color: $app-blue !important;
  opacity: 0.2;
}

::v-deep .v-text-field .v-label {
  font-weight: normal;
  color: $gray7;
}

::v-deep .theme--light.v-input input {
  color: $gray9;
}
</style>
