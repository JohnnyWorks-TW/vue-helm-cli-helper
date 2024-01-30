<template>
  <div>
    <div class="package-info-box" v-if="packageInfo">
      <img :src="logoImageUrl" alt="logo" class="logo" />
      <div>
        <span class="title">{{ packageInfo?.repository?.name }}/{{ packageInfo?.name }}</span>
        <span class="offical-mark" v-if="packageInfo?.official">✅</span>
        <span
          class="stars-mark"
          v-if="showDetails && packageInfo?.stars"
        >{{ packageInfo?.stars }} ⭐️</span>

        <span
          class="organization"
        >{{ $t('organization') }} {{ packageInfo?.repository?.organization_display_name }}</span>
        <span class="description" v-if="showDetails">{{ packageInfo?.description }}</span>
        <span class="text" v-if="showDetails">{{ $t('app-version') }}{{ packageInfo?.app_version }}</span>
        <span class="text" v-if="showDetails">{{ $t('helm-chart-version') }}{{ packageInfo?.version }}</span>
        <span class="text" v-if="showDetails">{{ $t('repository') }}{{ packageInfo?.repository?.url }}</span>
        <span class="text" v-if="showDetails">{{ $t('license') }}{{ packageInfo?.license }}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    packageInfo: {
      type: Object,
      default: null
    },
    showDetails: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    logoImageUrl() {
      if (!this.packageInfo?.logo_image_id) {
        return 'https://artifacthub.io/static/media/placeholder_pkg_helm.png';
      }
      return `https://artifacthub.io/image/${this.packageInfo?.logo_image_id}`;
    }
  }
}
</script>

<style scoped>
.package-info-box {
  align-items: center;
  margin-bottom: 10px;
  padding: 10px;
  border: 1px solid #444;
}

.logo {
  float: left;
  margin-right: 10px;
  width: 50px;
  height: 50px;
  object-fit: contain;
}

.title {
  font-size: 120%;
  font-weight: bold;
}

.description {
  display: block;
  font-size: 90%;
  margin-top: 10px;
  margin-bottom: 10px;
}

.offical-mark {
  display: inline-block;
  margin-left: 10px;
  font-size: 60%;
}

.stars-mark {
  display: inline-block;
  margin-left: 10px;
  border: 1px solid #444;
  padding: 0px 5px;
  font-size: 60%;
}

.organization {
  display: block;
  font-size: 80%;
  margin-top: 0px;
  margin-bottom: 10px;
}

.text {
  font-size: 80%;
  display: block;
  margin-top: 5px;
}
</style>