<template>
  <directory-list>
    <template v-slot:item="{ item }">
      <v-list-item
        :key="item.id"
        :to="item.links.href"
        nuxt
      >
        {{ item.properties.libelle }}
      </v-list-item>
    </template>
  </directory-list>
</template>

<script>
import i18nMixin from "~/components/mixins/i18n";
import DirectoryList from "~/components/directory_list";

export default {
  components: { DirectoryList },

  mixins: [i18nMixin],

  head() {
    return {
      title: `${this.$route.params.departements} - ${this.$t(this.title)} - ${this.brand}`,
      meta: [
        { hid: "robots", name: "robots", content: "noindex, follow" },
      ]
    };
  }
};
</script>
