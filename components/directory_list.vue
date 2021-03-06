<template>
  <div class="directory">
    <h1 class="directory-title">{{ $t(title) }}</h1>
    <v-divider v-if="relatedLinks.length > 0" />
    <v-list>
      <v-list-item
        v-for="link in relatedLinks"
        :key="link.title"
        :href="link.href"
        :rel="link.rel"
      >{{ $t(link.title) }}</v-list-item>
    </v-list>
    <v-divider />
    <v-list>
      <v-list-item v-for="item in items" :key="item.id" :to="itemLink(item)" nuxt>
        <div
          v-for="label in itemLabels(item, propertyLabel)"
          :key="label.text"
          class="directory-item-property"
        >
          <span
            v-if="label.translation"
          >{{ $te(`${label.translation}${label.text}`) ? $t(`${label.translation}${label.text}`): $t(`${label.translation}other`) }}</span>
          <span v-else>{{ label.text }}</span>
        </div>
      </v-list-item>
    </v-list>
  </div>
</template>

<script>
import { apiUrl } from "~/config.json";
import i18nMixin from "./mixins/i18n";

export default {
  mixins: [i18nMixin],

  computed: {
    title() {
      return this.json?.title;
    },
    items() {
      return this.json?.data;
    },
    relatedLinks() {
      return (this.json?.links || [])
        .filter(link => link.rel !== "self")
        .map(link => {
          const href = link.href.replace("/directory", "/annuaire");
          return { ...link, href };
        });
    }
  },
  methods: {
    itemLink(item) {
      return item.links.href.replace("/directory", "/annuaire");
    },
    itemLabels: (item, attrs) => {
      if (!attrs) {
        return [{ text: item.properties }];
      } else {
        return attrs
          .map(attr => {
            return {
              text: attr.key
                ? item.properties[attr.key.trim()]
                : item.properties,
              translation: attr.translation
            };
          })
          .filter(value => value && value.text);
      }
    }
  },
  fetchData({ region, departement, commune, category, query }) {
    const url =
      apiUrl +
      "/directory" +
      (region ? "/" + region : "") +
      (departement ? "/" + departement : "") +
      (commune ? "/" + commune : "") +
      (category ? "/" + category : "") +
      (query && Object.keys(query).length > 0
        ? "?" +
          Object.keys(query)
            .map(key => `${key}=${query[key]}`)
            .join("&")
        : "");
    return fetch(url)
      .then(res => {
        return res.json();
      })
      .then(json => {
        const selfLink = json.links.filter(link => link.rel === "self");
        json.title = selfLink.length > 0 ? selfLink[0].title : "";
        return {
          json
        };
      });
  }
};
</script>
<style>
.directory {
  padding: 0 10rem;
}

.directory-title {
  text-align: center;
  padding: 1rem 0;
}

.directory-logo {
  height: 2rem;
  margin-right: 1rem;
}

.directory-item-property {
  margin-right: 1rem;
}
</style>
