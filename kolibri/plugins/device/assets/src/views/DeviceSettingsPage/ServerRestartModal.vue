<template>

  <KModal
    :title="$tr('serverRestart')"
    :submitText="coreString('continueAction')"
    :cancelText="coreString('cancelAction')"
    @submit="handleSubmit"
    @cancel="$emit('cancel')"
  >
    <p v-if="changedSetting === 'add'" class="description">
      {{ $tr('selectedPath', { path: path.path }) }}
    </p>
    <p class="description">
      {{ getMessage() }}
    </p>
    <div v-if="changedSetting === 'add' && path.writable === true">
      <KCheckbox
        :checked="confirmationChecked"
        :label="$tr('makePrimary')"
        :description="deviceString('primaryStorageLabel')"
        @change="confirmationChecked = $event"
      />
    </div>
  </KModal>

</template>


<script>

  import commonCoreStrings from 'kolibri.coreVue.mixins.commonCoreStrings';
  import commonDeviceStrings from '../commonDeviceStrings';

  export default {
    name: 'ServerRestartModal',
    mixins: [commonCoreStrings, commonDeviceStrings],
    props: {
      changedSetting: {
        type: String, // primary, remove, add, plugins
        required: true,
      },
      path: {
        type: Object,
        required: false,
        default: null,
      },
    },
    data() {
      return {
        confirmationChecked: false,
      };
    },
    methods: {
      getMessage() {
        let message = '';
        switch (this.changedSetting) {
          case 'primary':
            message = this.$tr('newPrimaryLocationRestartDescription');
            break;
          case 'remove':
            message = this.$tr('removeLocationRestartDescription');
            break;
          case 'add':
            message = this.$tr('newLocationRestartDescription');
            break;
          case 'plugin':
            message = this.deviceString('newEnabledPluginsState');
            break;
        }
        // message is a separate sentence, concatenating them is not problematic
        return message + this.$tr('serverRestartDescription');
      },
      handleSubmit() {
        if (this.changedSetting === 'add') {
          this.$emit('submit', this.confirmationChecked);
        } else {
          this.$emit('submit');
        }
      },
    },
    $trs: {
      serverRestart: {
        message: 'Server restart',
        context: 'Prompt for removing a storage location.',
      },
      newLocationRestartDescription: {
        message: 'Server needs to restart to add a new storage location.',
        context: 'Reason to restart the server.',
      },
      newPrimaryLocationRestartDescription: {
        message:
          'Server needs to restart to add a new storage location. Anyone using Kolibri on this server at this time will temporarily be unable to access it.',
        context: 'Reason to restart the server.',
      },
      removeLocationRestartDescription: {
        message: 'Removing a storage location will restart this server.',
        context: 'Reason to restart the server.',
      },
      serverRestartDescription: {
        message:
          'Anyone using Kolibri on this server right now will temporarily be unable to use it.',
        context: 'Description for restarting the server.',
      },
      /* eslint-disable kolibri/vue-no-unused-translations */
      enableOrDisableRequiresRefresh: {
        message:
          'When you enable or disable a page, Kolibri will restart, and you must refresh the browser to see the changes. Anyone using Kolibri on this server at this time will be temporarily disconnected.',
        context: 'Changing enabled pages',
      },
      serverNeedsRestart: {
        message:
          'The server will need to restart. Do this during low server usage times to avoid disruptions.',
        context: '',
      },
      /* eslint-enable */
      selectedPath: {
        message: 'Selected: {path}',
        context: 'Label for the selected path.',
      },
      makePrimary: {
        message: 'Make this the primary storage location',
        context: 'Checkbox to make primary storage location.',
      },
    },
  };

</script>


<style lang="scss" scoped>

  .description {
    margin-top: 0;
  }

</style>
