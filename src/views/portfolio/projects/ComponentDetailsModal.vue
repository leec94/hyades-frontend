<template>
  <b-modal id="componentDetailsModal" size="md" hide-header-close no-stacking :title="$t('message.component_details')">
    <b-tabs class="body-bg-color" style="border:0;padding:0">
      <b-tab class="body-bg-color" style="border:0;padding:0" active>
        <template v-slot:title><i class="fa fa-cube"></i> {{ $t('message.identity') }}</template>
        <b-card>
          <b-input-group-form-input id="component-name-input" input-group-size="mb-3" type="text" v-model="component.name"
                                    lazy="true" required="true" feedback="true" autofocus="false"
                                    :label="$t('message.component_name')" :tooltip="this.$t('message.component_name_desc')"
                                    :feedback-text="$t('message.required_component_name')"
                                    :readonly="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
          <b-input-group-form-input id="component-version-input" input-group-size="mb-3" type="text" v-model="component.version"
                                    lazy="true" required="true" feedback="true" autofocus="false"
                                    :label="$t('message.version')" :tooltip="this.$t('message.component_version_desc')"
                                    :feedback-text="$t('message.required_component_version')"
                                    :readonly="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
          <b-input-group-form-input id="component-group-input" input-group-size="mb-3" type="text" v-model="component.group"
                                    required="false" :label="$t('message.component_namespace_group_vendor')"
                                    :tooltip="this.$t('message.component_group_desc')"
                                    :readonly="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
          <b-input-group-form-input id="component-purl-input" input-group-size="mb-3" type="text" v-model="component.purl"
                                    required="false" :label="$t('message.package_url_full')"
                                    :tooltip="this.$t('message.component_package_url_desc')"
                                    :readonly="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
          <b-input-group-form-input id="component-cpe-input" input-group-size="mb-3" type="text" v-model="component.cpe"
                                    required="false" :label="$t('message.cpe_full')"
                                    :tooltip="$t('message.component_cpe_desc')"
                                    :readonly="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
        </b-card>
      </b-tab>
      <b-tab>
        <template v-slot:title><i class="fa fa-caret-square-o-right"></i> {{ $t('message.extended') }}</template>
        <b-card>
          <b-input-group-form-select id="component-classifier-input" required="true"
                                     v-model="component.classifier" :options="availableClassifiers"
                                     :label="$t('message.classifier')" :tooltip="$t('message.component_classifier_desc')"
                                     :disabled="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
          <b-input-group-form-input id="component-filename-input" input-group-size="mb-3" type="text" v-model="component.filename"
                                    required="false" :label="$t('message.filename')" :tooltip="$t('message.component_filename_desc')"
                                    :readonly="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
          <b-form-group
            id="component-description-form-group"
            :label="this.$t('message.description')"
            label-for="component-description-input">
            <b-form-textarea id="component-description-description" v-model="component.description" rows="3"
                             :readonly="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
          </b-form-group>
        </b-card>
      </b-tab>
      <b-tab>
        <template v-slot:title><i class="fa fa-balance-scale"></i> {{ $t('message.legal') }}</template>
        <b-card>
          <b-input-group-form-select id="component-license-input" required="false"
                                     v-model="selectedLicense" :options="selectableLicenses"
                                     :label="$t('message.license')" :tooltip="$t('message.component_spdx_license_desc')"
                                     :disabled="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
          <b-form-group
            id="component-copyright-form-group"
            :label="this.$t('message.copyright')"
            label-for="component-copyright-input">
            <b-form-textarea id="component-description-description" v-model="component.copyright" rows="3"
                             :readonly="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
          </b-form-group>
        </b-card>
      </b-tab>
      <b-tab>
        <template v-slot:title><i class="fa fa-barcode"></i> {{ $t('message.hashes') }}</template>
        <b-card>
          <b-input-group-form-input id="component-md5-input" input-group-size="mb-3" type="text" v-model="component.md5"
                                    required="false" :label="$t('message.md5')" :tooltip="$t('message.component_hash_desc')"
                                    :readonly="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
          <b-input-group-form-input id="component-sha1-input" input-group-size="mb-3" type="text" v-model="component.sha1"
                                    required="false" :label="$t('message.sha1')" :tooltip="$t('message.component_hash_desc')"
                                    :readonly="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
          <b-input-group-form-input id="component-sha256-input" input-group-size="mb-3" type="text" v-model="component.sha256"
                                    required="false" :label="$t('message.sha256')" :tooltip="$t('message.component_hash_desc')"
                                    :readonly="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
          <b-input-group-form-input id="component-sha512-input" input-group-size="mb-3" type="text" v-model="component.sha512"
                                    required="false" :label="$t('message.sha512')" :tooltip="$t('message.component_hash_desc')"
                                    :readonly="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
          <b-input-group-form-input id="component-sha3256-input" input-group-size="mb-3" type="text" v-model="component.sha3_256"
                                    required="false" :label="$t('message.sha3_256')" :tooltip="$t('message.component_hash_desc')"
                                    :readonly="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
          <b-input-group-form-input id="component-sha3512-input" input-group-size="mb-3" type="text" v-model="component.sha3_512"
                                    required="false" :label="$t('message.sha3_512')" :tooltip="$t('message.component_hash_desc')"
                                    :readonly="this.isNotPermitted(PERMISSIONS.PORTFOLIO_MANAGEMENT)" />
        </b-card>
      </b-tab>
    </b-tabs>
    <template v-slot:modal-footer="{ cancel }">
      <b-button size="md" variant="outline-danger" @click="deleteComponent()" v-permission="PERMISSIONS.PORTFOLIO_MANAGEMENT">{{ $t('message.delete') }}</b-button>
      <b-button size="md" variant="secondary" @click="cancel()">{{ $t('message.close') }}</b-button>
      <b-button size="md" variant="primary" @click="updateComponent()" v-permission="PERMISSIONS.PORTFOLIO_MANAGEMENT">{{ $t('message.update') }}</b-button>
    </template>
  </b-modal>
</template>

<script>
  import BInputGroupFormInput from "../../../forms/BInputGroupFormInput";
  import BInputGroupFormSelect from "../../../forms/BInputGroupFormSelect";
  import permissionsMixin from "../../../mixins/permissionsMixin";

  export default {
    name: "ComponentDetailsModal",
    mixins: [permissionsMixin],
    components: {
      BInputGroupFormInput,
      BInputGroupFormSelect
    },
    props: {
      component: Object
    },
    data() {
      return {
        availableClassifiers: [
          { value: 'APPLICATION', text: 'Application' },
          { value: 'FRAMEWORK', text: 'Framework' },
          { value: 'LIBRARY', text: 'Library' },
          { value: 'OPERATING_SYSTEM', text: 'Operating System' },
          { value: 'DEVICE', text: 'Device' },
          { value: 'FILE', text: 'File' }
        ],
        selectableLicenses: [],
        selectedLicense: ''
      }
    },
    beforeMount() {
      this.retrieveLicenses();
    },
    beforeUpdate() {
      console.log(this.component);
    },
    methods: {
      updateComponent: function() {
        this.$root.$emit('bv::hide::modal', 'componentDetailsModal');
        let url = `${this.$api.BASE_URL}/${this.$api.URL_COMPONENT}`;
        this.axios.post(url, {
          uuid: this.component.uuid,
          name: this.component.name,
          version: this.component.version,
          group: this.component.group,
          description: this.component.description,
          license: this.selectedLicense,
          filename: this.component.filename,
          classifier: this.component.classifier,
          purl: this.component.purl,
          cpe: this.component.cpe,
          copyright: this.component.copyright,
          md5: this.component.md5,
          sha1: this.component.sha1,
          sha256: this.component.sha256,
          sha512: this.component.sha512,
          sha3_256: this.component.sha3_256,
          sha3_512: this.component.sha3_512
        }).then((response) => {
          this.$emit('componentUpdated', response.data);
          this.$toastr.s(this.$t('message.component_updated'));
        }).catch((error) => {
          this.$toastr.w(this.$t('condition.unsuccessful_action'));
        });
      },
      deleteComponent: function() {
        this.$root.$emit('bv::hide::modal', 'componentDetailsModal');
        let url = `${this.$api.BASE_URL}/${this.$api.URL_COMPONENT}/` + this.component.uuid;
        this.axios.delete(url).then((response) => {
          this.$toastr.s(this.$t('message.component_deleted'));
          this.$router.replace({ name: "Components" });
        }).catch((error) => {
          this.$toastr.w(this.$t('condition.unsuccessful_action'));
        });
      },
      retrieveLicenses: function() {
        let url = `${this.$api.BASE_URL}/${this.$api.URL_LICENSE_CONCISE}`;
        this.axios.get(url).then((response) => {
          for (let i = 0; i < response.data.length; i++) {
            let license = response.data[i];
            this.selectableLicenses.push({value: license.licenseId, text: license.name});
            if (this.component.resolvedLicense && this.component.resolvedLicense.uuid === license.uuid ) {
              this.selectedLicense = license.licenseId;
            }
          }
        }).catch((error) => {
          this.$toastr.w(this.$t('condition.unsuccessful_action'));
        });
      }
    }
  }
</script>

<style scoped>
  .tab-content .tab-pane{
    padding: 0 !important;
  }
  .tab-content {
    border: 0 !important;
  }
  .card {
    border:0;
    padding:0;
    margin-bottom:0;
  }
</style>