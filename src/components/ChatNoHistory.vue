<template>
  <v-row no-gutters>
    <v-col cols="12">
      <v-card>
        <v-img
          :src="noHistoryImage"
          class="elevation-2"
        ></v-img>
        <v-card-title primary-title>
          <v-card-text>
            <div class="text-center">
              <h3 class="headline mb-2 text-center">{{ $t('no.chat.history.title') }}</h3>
              <div class="text-center">{{ $t('no.chat.history.body') }}</div>
            </div>
          </v-card-text>
        </v-card-title>
        <v-row
          justify="center"
          class="pb-3"
        >
          <v-card-actions>
            <v-btn
              dark
              color="primary"
              :to="{name: 'chat'}"
            >{{ $t('menu.chat') }}</v-btn>
            <v-btn
              dark
              color="primary"
              :to="{name: 'help'}"
            >{{ $t('menu.help') }}</v-btn>
            <v-btn
              dark
              color="primary"
              :to="{name: 'about'}"
            >{{ $t('menu.about') }}</v-btn>
          </v-card-actions>
        </v-row>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  computed: {
    noHistoryImage() {
      return require("../assets/history.png");
    }
  }
};
</script>
