<template>
  <v-card
    class="mx-auto mt-2"
    max-width="400"
  >
    <v-img v-if="config.imageUrl"
      class="white--text align-end"
      height="200px"
      :src="config.imageUrl"
    >
      <v-card-title class="cardTitleBackground" v-if="config.title">{{ config.title }}</v-card-title>
    </v-img>
    <v-card-title v-else-if="config.title" class="mb-2">{{ config.title }}</v-card-title>

    <v-card-subtitle v-if="config.subTitle" class="pb-1">{{ config.subTitle }}</v-card-subtitle>

    <v-card-text v-if="config.bodyText" class="text--primary">
      {{ config.bodyText }}
    </v-card-text>

    <v-card-text v-if="config.chips" class="my-0 py-0">
      <v-chip-group
        v-model="chipSelectionIndex"
        active-class="secondary white--text"
        column
      >
        <v-chip v-for="(chip, index) in config.chips" :key="'chip' + index">{{ chip.name }}</v-chip>
      </v-chip-group>
    </v-card-text>

    <v-card-actions v-if="config.actions">
      <v-spacer></v-spacer>
      <v-btn :disabled="config.chips && chipSelectionIndex === null" v-for="(action, index) in config.actions" :key="'action' + index"
        small color="primary" @click="actionClicked(action)"
      >
        {{ action.name }}
      </v-btn>
    </v-card-actions>
  </v-card>
</template>
<script>
export default {
  name: "Card",
  props: ["item"],
  data() {
    return {
      chipSelectionIndex : null
    };
  },
  computed: {
    config() {
      let theConfig = decodeURIComponent(this.item.teneoResponse.extraData.displayCard);
      return JSON.parse(theConfig)
    }
  },
  methods: {
    actionClicked(action) {
      let responseText = "";
      let responseParameters = "";
      if (this.chipSelectionIndex !== null) {
          let selectedChip = this.config.chips[this.chipSelectionIndex];
          responseText = selectedChip.name;
          responseParameters = "&" + selectedChip.params;
      } else {
          responseText = action.name;
      }
      responseParameters += "&" + action.params;
      this.$store.commit("SHOW_PROGRESS_BAR");
      this.$store.commit("SET_USER_INPUT", responseText);
      let optionClickParam = "&isClick=true";
      this.$store
        .dispatch(
          "sendUserInput",
          responseParameters !== ''
            ? responseParameters + optionClickParam
            : optionClickParam
        )
        .then(() => {
          console.log("Card info sent to Teneo");
        });
    }
  },
};
</script>

<style>
.cardTitleBackground {
  background-color: rgba(17,18,25,0.5);
}

</style>