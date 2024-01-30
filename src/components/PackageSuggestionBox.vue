<template>
  <div class="package-info-box" v-if="packageInfo">
    <img :v-if="logoImageUrl" :src="logoImageUrl" class="logo" />
    <div>
      <span class="title">{{ packageInfo?.repository?.name }}/{{ packageInfo?.name }}</span>
      <span class="offical-mark" v-if="packageInfo?.official">✅</span>
      <span
        class="organization"
      >Organization: {{ packageInfo?.repository?.organization_display_name }}</span>
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
  width: fit-content;
  display: flex;
}
.logo {
  margin-left: 10px;
  margin-top: 3px;
  margin-right: 10px;
  vertical-align: middle;
  width: 30px;
  height: 30px;
  object-fit: contain;
}

.title {
  font-size: 100%;
  font-weight: bold;
}

.organization {
  display: block;
  font-size: 80%;
  text-align: left;
  margin-top: 0px;
  margin-bottom: 10px;
}

.offical-mark {
  display: inline-block;
  margin-left: 10px;
  font-size: 60%;
}
</style>