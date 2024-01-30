<template>
  <div id="app">
    <h2>
      <img alt="helm" src="https://helm.sh/img/helm.svg" width="30" />
      {{ $t('app-name') }}
    </h2>
    <select class="locale" v-model="locale">
      <option
        :key="`locale-${item.lang}`"
        :value="item.lang"
        v-for="item in localeOptions"
      >{{ item.name }}</option>
    </select>
    <span class="desc">{{ $t('desc') }}</span>
    <div id="search-box">
      <input
        :placeholder="$t('search-box-hint')"
        @input="debouncedUpdateSuggestions"
        @keyup.enter="fetchData"
        ref="searchBoxInput"
        v-model="searchQuery"
      />
      <button @click="fetchData">{{ $t('search') }}</button>
    </div>
    <div :style="suggestionBoxStyle" class="suggestion-box" v-if="suggestions?.length">
      <package-suggestion-box
        :key="index"
        :packageInfo="suggestion"
        @click="selectSuggestion(suggestion)"
        v-for="(suggestion, index) in suggestions"
      />
    </div>
    <div v-if="packageInfo">
      <cli-suggestions :packageInfo="packageInfo" />
    </div>
  </div>
  <div v-if="errorMessage">{{ errorMessage }}</div>
</template>

<script>
import axios from 'axios';
import { debounce } from 'lodash';
import CliSuggestions from './components/CLISuggestions.vue';
import PackageSuggestionBox from './components/PackageSuggestionBox.vue';
import { useI18n } from 'vue-i18n';

export default {
  components: { CliSuggestions, PackageSuggestionBox },
  setup() {
    const { locale } = useI18n();
    return {
      locale,
      localeOptions: [
        {
          lang: 'en',
          name: 'English'
        },
        {
          lang: 'zh',
          name: '繁體中文'
        }
      ]
    };
  },
  created() {
    this.debouncedUpdateSuggestions = debounce(this.updateSuggestions, 300);
  },
  data() {
    return {
      testObj: {
        "package_id": "d5dad873-8e69-411b-8391-933d4391988a",
        "name": "prometheus",
        "normalized_name": "prometheus",
        "category": 4,
        "logo_image_id": "0503add5-3fce-4b63-bbf3-b9f649512a86",
        "stars": 365,
        "official": true,
        "description": "Prometheus is a monitoring system and time series database.",
        "version": "25.10.0",
        "app_version": "v2.49.1",
        "license": "Apache-2.0",
        "deprecated": false,
        "signed": false,
        "security_report_summary": {
          "low": 0,
          "high": 2,
          "medium": 13,
          "unknown": 0,
          "critical": 0
        },
        "all_containers_images_whitelisted": false,
        "production_organizations_count": 7,
        "ts": 1705909906,
        "repository": {
          "url": "https://prometheus-community.github.io/helm-charts",
          "cncf": true,
          "kind": 0,
          "name": "prometheus-community",
          "official": false,
          "display_name": "prometheus-community",
          "repository_id": "61b55c4e-f8ef-4a51-9595-695560540ca3",
          "scanner_disabled": false,
          "organization_name": "prometheus",
          "verified_publisher": true,
          "organization_display_name": "Prometheus"
        }
      },
      searchQuery: '',
      suggestions: [],
      packageInfo: null,
      errorMessage: '',
      dataFetched: false
    };
  },
  mounted() {
    document.title = this.$t('app-name');
    this.updateSearchBoxPosition();
    window.onresize = () => {
      this.updateSearchBoxPosition();
    };
  },
  computed: {
    suggestionBoxStyle() {
      return {
        left: `${this.searchBoxPosition.left}px`,
        width: `${this.searchBoxPosition.width - 20}px`,
        position: 'absolute',
        backgroundColor: '#fff',
        border: '1px solid #ddd',
        padding: '10px',
      };
    },
  },
  methods: {
    updateSearchBoxPosition() {
      const searchBoxInput = this.$refs.searchBoxInput;
      const rect = searchBoxInput.getBoundingClientRect();

      this.searchBoxPosition = {
        top: rect.top + window.scrollY,
        left: rect.left + window.scrollX,
        width: rect.width,
      };
    },
    selectSuggestion(suggestion) {
      this.searchQuery = suggestion.name;
      this.packageInfo = suggestion;
      this.suggestions = [];
      this.errorMessage = '';
    },
    fetchData() {
      if (this.searchQuery === '') {
        this.errorMessage = this.$t('keyword-empty-message');
        return;
      }

      axios.get(`https://artifacthub.io/api/v1/packages/search?ts_query_web=${this.searchQuery}&facets=true&sort=relevance&limit=2&offset=0`)
        .then(response => {
          // Get the first package's data
          if (response?.status !== 200) {
            throw new Error('Invalid response from server');
          }
          if (response?.data?.packages?.length === 0) {
            throw new Error(this.$t('no-package-found'));
          }
          this.packageInfo = response?.data?.packages[0];
          this.dataFetched = true;
          this.suggestions = [];
          this.errorMessage = '';
        })
        .catch(error => {
          console.error('Error fetching data:', error);
          this.dataFetched = false;
          this.errorMessage = error.message;
        });
    },
    updateSuggestions() {
      if (this.searchQuery === '') {
        this.suggestions = [];
        return;
      }

      axios.get(`https://artifacthub.io/api/v1/packages/search?ts_query_web=${this.searchQuery}&facets=false&sort=relevance&limit=5&offset=0`)
        .then(response => {
          this.suggestions = response?.data?.packages;
          this.errorMessage = '';
        }).catch(error => {
          console.error('Error fetching data:', error);
          this.suggestions = [];
        });
    }
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
  margin: 60px 12px;
}

h2 {
  margin: 0 0 10px;
  text-align: center;
}

#search-box {
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

#search-box input {
  margin: 20px 0;
  padding: 10px;
  width: 300px;
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

.locale {
  margin: 0 0 10px;
  padding: 5px;
  float: right;
}
</style>