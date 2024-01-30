<template>
  <div v-if="packageInfo">
    <h3>{{ $t('version-info-title') }}</h3>
    <div>
      <package-info-box :packageInfo="packageInfo" :showDetails="true" />
    </div>
    <div class="switch-bar">
      <div class="offline-switch switch">
        <input id="switch" type="checkbox" v-model="showOfflineInstall" />
        <label for="switch">{{ $t('offline-install-mode') }}</label>
      </div>
      <div class="markdown-plain-switch switch">
        <input id="switch" type="checkbox" v-model="showMarkdownPlain" />
        <label for="switch">{{ $t('show-orginal-text') }}</label>
      </div>
    </div>
    <markdown-block :source="generateMarkdownText" v-if="!showMarkdownPlain" />
    <textarea :value="generateMarkdownText" cols="90" readonly rows="40" v-if="showMarkdownPlain"></textarea>

    <h3>{{ $t('reference') }}</h3>
    <a :href="artifactHubUrl" target="_blank">{{ artifactHubUrl }}</a>
  </div>
</template>

<script>
import MarkdownBlock from './MarkdownBlock.vue';
import PackageInfoBox from './PackageInfoBox.vue';
import { useI18n } from 'vue-i18n';
export default {
  components: { PackageInfoBox, MarkdownBlock },
  setup() {
    return {
      $t: useI18n().t,
    };
  },
  props: {
    packageInfo: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      showOfflineInstall: false,
      showMarkdownPlain: false,
    };
  },
  computed: {
    artifactHubUrl() {
      return `https://artifacthub.io/packages/helm/${this.packageInfo?.repository?.name}/${this.packageInfo?.name}`;
    },
    releaseName() {
      let sp = this.packageInfo?.name.split(/[-_]/);
      if (sp.length > 1) {
        sp = sp.slice(0, sp.length - 1);
        return sp[0];
      }
      return this.packageInfo?.name;
    },
    packName() {
      if (this.showOfflineInstall && this.packageInfo?.name && this.packageInfo?.version) {
        return `${this.packageInfo?.name}-${this.packageInfo?.version}.tgz`;
      }
      return `${this.packageInfo?.repository?.name}/${this.packageInfo?.name}`;
    },
    generateMarkdownText() {
      var text = '';

      if (!this.showOfflineInstall) {
        text += `
### ${this.$t('add')} helm repo

\`\`\`bash
helm repo add ${this.packageInfo?.repository?.name} ${this.packageInfo?.repository?.url}
helm repo update
\`\`\``;
      }
      text += `
### ${this.$t('show')} helm charts ${this.$t('values')}

\`\`\`bash
helm show values ${this.packName} --version ${this.packageInfo?.version} > ${this.releaseName}-values.yaml
\`\`\`

### helm ${this.$t('install')} ${this.packageInfo?.name}

\`\`\`bash
helm install ${this.releaseName} ${this.packName} -n ${this.releaseName} --version ${this.packageInfo?.version} -f ${this.releaseName}-values.yaml
\`\`\`

### helm ${this.$t('upgrade')} ${this.packageInfo?.name}

\`\`\`bash
helm upgrade ${this.packageInfo?.name} ${this.packName} -n ${this.releaseName} --version ${this.packageInfo?.version} -f ${this.releaseName}-values.yaml
\`\`\`

### helm ${this.$t('delete')} ${this.packageInfo?.name}

\`\`\`bash
helm uninstall ${this.packageInfo?.name} -n ${this.releaseName}
\`\`\`

### helm ${this.$t('generate')} ${this.packageInfo?.name} ${this.$t('templeate')}

\`\`\`bash
helm template ${this.releaseName} ${this.packName} -n ${this.releaseName} --version ${this.packageInfo?.version} -f ${this.releaseName}-values.yaml --output-dir ./${this.releaseName}-yamls
\`\`\``;

      if (!this.showOfflineInstall) {
        text += `

### helm ${this.$t('download')} chart

\`\`\`bash
helm pull ${this.packName} --version ${this.packageInfo?.version}
\`\`\``;
      }
      return text;
    },

  }
};
</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-top: 60px;
}

h2 {
  margin: 0 0 10px;
  text-align: center;
}

#search_box {
  text-align: center;
}

.desc {
  display: block;
  text-align: center;
  margin-bottom: 20px;
}

h3 {
  margin: 20px 0 10px;
  text-align: left;
}

button {
  padding: 10px 20px;
  cursor: pointer;
}

pre {
  background-color: #f5f5f5;
  padding: 15px;
  text-align: left;
  overflow-y: scroll;
}

.suggestions {
  margin: 0;
  padding: 0;
  list-style-type: none;
  background-color: #fff;
  border: 1px solid #ddd;
}

.suggestions li {
  padding: 10px;
  cursor: pointer;
}

.suggestions li:hover {
  background-color: #f0f0f0;
}

.switch-bar {
  text-align: right;
}

.switch input {
  margin: 0 10px;
}

.switch {
  display: inline;
  text-align: right;
  margin: 10px 0;
}

.clear-both {
  clear: both;
}

textarea {
  width: 100%;
  resize: none;
  overflow: auto;
}
</style>
