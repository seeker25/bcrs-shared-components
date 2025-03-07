<template>
  <v-dialog
    v-model="dialog"
    content-class="confirm-dialog"
    :max-width="options.width"
    :style="{ zIndex: options.zIndex }"
    :persistent="options.persistent"
    :attach="attach"
    @keydown.esc="onClickCancel">

    <v-card>
      <v-card-title>{{ title }}</v-card-title>
      <v-card-text class="pre-wrap" v-show="!!message" v-html="message" />
      <v-divider class="my-0" v-show="!!options.yes || !!options.no || !!options.cancel" />
      <v-card-actions>
        <v-spacer />
        <v-btn text color="primary" class="dialog-yes-btn" v-if="!!options.yes"
          @click.native="onClickYes()">{{ options.yes }}</v-btn>
        <v-btn text color="primary" class="dialog-no-btn" v-if="!!options.no"
          @click.native="onClickNo()">{{ options.no }}</v-btn>
        <v-btn text color="primary" class="dialog-cancel-btn" v-if="!!options.cancel"
          @click.native="onClickCancel()">{{ options.cancel }}</v-btn>
      </v-card-actions>
    </v-card>

  </v-dialog>
</template>

<script lang="ts">
/**
 * Vuetify Confirm Dialog component
 * ref: https://gist.github.com/eolant/ba0f8a5c9135d1a146e1db575276177d
 *
 * Insert component where you want to use it:
 * <confirm ref="confirm"></confirm>
 *
 * Call it using promise:
 * this.$refs.confirm.open('Delete', 'Are you sure?', { color: 'red' }).then((confirm) => {})
 *
 * Or use await:
 * if (await this.$refs.confirm.open('Delete', 'Are you sure?', { color: 'red' })) {
 *   // yes
 * }
 * else {
 *   // cancel
 * }
 *
 * Alternatively you can place it in main App component and access it globally via this.$root.$confirm
 * <template>
 *   <v-app>
 *     ...
 *     <confirm ref="confirm"></confirm>
 *   </v-app>
 * </template>
 *
 * mounted() {
 *   this.$root.$confirm = this.$refs.confirm.open
 * }
 */
import { Component, Prop, Vue } from 'vue-property-decorator'

interface OptionsObject {
  width?: number | string,
  zIndex?: number,
  persistent?: boolean,
  yes?: string,
  no?: string,
  cancel?: string
}

@Component({})
export default class ConfirmDialog extends Vue {
  /** Prop to provide attachment selector. */
  @Prop() private attach: string

  /** Whether the subject dialog is currently displayed. */
  private dialog: boolean = false

  /** The Yes/No click handler for the returned promise. */
  private resolve: Function = null

  /** The Cancel click handler for the returned promise. */
  private reject: Function = null

  /** The dialog's title. */
  private title: string = null

  /** The dialog's message. */
  private message: string = null

  /** The dialog's default options. */
  private options: OptionsObject = {
    width: 400,
    zIndex: 200,
    persistent: false,
    yes: 'Yes',
    no: 'No',
    cancel: 'Cancel'
  }

  /**
   * Opens the modal with the specified parameters.
   * @param title   the dialog's title to display
   * @param message the dialog's message to display
   * @param options the dialog's options to use
   * @returns       a promise to subscribe to
   */
  open (title: string, message: string, options: OptionsObject): Promise<{}> {
    this.dialog = true
    this.title = title
    this.message = message
    this.options = Object.assign(this.options, options)

    return new Promise((resolve, reject) => {
      this.resolve = resolve
      this.reject = reject
    })
  }

  /** Handler for Yes button. */
  private onClickYes (): void {
    this.resolve(true)
    this.dialog = false
  }

  /** Handler for No button. */
  private onClickNo (): void {
    this.resolve(false)
    this.dialog = false
  }

  /** Handler for Cancel button. */
  private onClickCancel (): void {
    this.reject()
    this.dialog = false
  }
}
</script>

<style lang="scss" scoped>
@import '@/assets/styles/theme.scss';

.v-card__text {
  color: $gray7 !important;
}

.v-btn {
  text-transform: unset;
}

// bold the primary action button
.dialog-yes-btn {
  font-weight: bold;
}
</style>
