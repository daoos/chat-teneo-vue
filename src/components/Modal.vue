<template>
  <span>
    <CustomModal
      :items="customModalItems"
      :toolbarWidth="toolbarWidth"
    ></CustomModal>
    <!-- Display Pusher Message -->
    <!-- <Pusher
      :displayPusherMessage="displayPusherMessage"
      :pusherMessage="pusherMessage"
    ></Pusher> -->

    <!-- show normal message -->
    <v-dialog
      v-model="showModal"
      leave-absolute
      scrollable
      persistent
      content-class="teneo-modal"
      hide-overlay
      fullscreen
    >
      <v-row no-gutters>
        <v-col cols="12">

          <v-toolbar
            dark
            color="primary"
            height="64"
            :class="toolbarWidth"
          >
            <v-btn
              fab
              small
              @click="hideModal"
              color="secondary"
            >
              <v-icon
                dark
                medium
              >mdi-close</v-icon>
            </v-btn>
            <v-toolbar-title>{{ $t('more.info.title') }}</v-toolbar-title>
          </v-toolbar>
        </v-col>
        <v-col cols="12">
          <v-card
            class="modal-height teneo-modal-card"
            :class="{'dark-scroll': dark, 'light-scroll': !dark}"
            tile
          >

            <!-- YouTube -->
            <YouTube :videoId="youTubeVideoId"></YouTube>

            <!-- Vimeo -->
            <Vimeo :videoId="vimeoVideoId"></Vimeo>

            <!-- Audio -->
            <Audio :url="audioUrl"></Audio>

            <!-- Misc Video -->
            <Video
              :url="videoUrl"
              :type="videoType"
            ></Video>

            <!-- Gogle Map -->
            <Map
              v-if="address"
              :address="address"
            ></Map>

            <v-container class="modal-container">

              <!-- show an image if available -->
              <ImageAnimation :url="imageUrl"></ImageAnimation>

              <!-- show a carousel of images if available -->
              <Carousel :imageItems="images"></Carousel>

              <!-- display the modal title and sub-title -->
              <v-row
                align="start"
                justify="start"
                class="mx-1"
              >
                <v-card-title primary-title>
                  <!-- Main Title -->
                  <div
                    class="modal-headline"
                    v-if="title"
                    v-html="title"
                  ></div>
                  <!-- Sub-Title -->
                  <span
                    class="grey--text"
                    v-if="subTitle"
                    v-html="subTitle"
                  ></span>
                </v-card-title>
              </v-row>

              <v-row
                align="start"
                justify="center"
              >
                <!-- show the close modal button -->
                <v-card-actions>
                  <!-- Yes there are keyboard shortcuts to close the modal window -->
                  <v-btn
                    color="primary"
                    v-shortkey="['ctrl', 'alt', 'arrowleft']"
                    @shortkey.native="hideModal"
                    @click.native="hideModal"
                  >{{ $t('back.to.chat.button') }}
                  </v-btn>
                </v-card-actions>
              </v-row>

              <!-- Show the body text, flight itineary, and any tables if available -->
              <div
                class=" mt-3"
                v-if="itinerary || bodyText || transactionItems.length || tableRows.length"
              >
                <!-- Show the flight itinerary -->
                <FlightItinerary :itinerary="itinerary"></FlightItinerary>

                <!-- show the body text -->
                <v-card-text
                  class="cardText"
                  id="chat-modal-html"
                  v-if="bodyText"
                  v-html="bodyText"
                  scrollable
                ></v-card-text>

                <!-- data tables -->
                <v-row
                  class="fill-height mx-1"
                  v-if="transactionItems.length > 0 || tableRows.length > 0"
                  align="end"
                  justify="start"
                >
                  <!-- table title -->
                  <v-row v-if="tableTitle">
                    <v-col
                      cols="8"
                      class="ml-4"
                    >
                      <h3>{{tableTitle}}</h3>
                    </v-col>
                  </v-row>
                  <v-spacer v-else></v-spacer>
                  <!-- show a search input box for the table -->
                  <v-col
                    cols="4"
                    v-if="tableEnableSearch"
                    class="mr-2"
                  >
                    <v-text-field
                      v-model="search"
                      append-icon="mdi-table-search"
                      label="Search"
                      single-line
                      hide-details
                    ></v-text-field>
                  </v-col>
                </v-row>

                <!-- Show the MyBank Transactions as a Table -->
                <MyBankTransactions
                  :headers="transactionHeaders"
                  :items="transactionItems"
                  :search="search"
                ></MyBankTransactions>

                <!-- Show a Generic Data Table -->
                <Table
                  :headers="tableHeaders"
                  :items="tableRows"
                  :search="search"
                  :footer="tableFooter"
                  :rowsPerPage="tableRowsPerPage"
                ></Table>

              </div>
              <v-spacer></v-spacer>
            </v-container>

          </v-card>
        </v-col>
      </v-row>
    </v-dialog>

  </span>
</template>

<script>
// import Audio from "./Audio";
// import Carousel from "./Carousel";
// import CustomModal from "./CustomModal";
// import FlightItinerary from "./FlightItinerary";
// import ImageAnimation from "./ImageAnimation";
// import MyBankTransactions from "./MyBankTransactions";
// import Pusher from "./Pusher";
// import Table from "./Table";
// import Video from "./Video";
// import Map from "./Map";
// import Vimeo from "./Vimeo";
// import YouTube from "./YouTube";
import { mapGetters } from "vuex";

export default {
  components: {
    Audio: () => import("./Audio"),
    Carousel: () => import("./Carousel"),
    CustomModal: () => import("./CustomModal"),
    FlightItinerary: () => import("./FlightItinerary"),
    ImageAnimation: () => import("./ImageAnimation"),
    MyBankTransactions: () => import("./MyBankTransactions"),
    Map: () => import("./Map"),
    // Pusher,
    Table: () => import("./Table"),
    Video: () => import("./Video"),
    Vimeo: () => import("./Vimeo"),
    YouTube: () => import("./YouTube")
  },
  data() {
    return {
      actions: "",
      audioType: "",
      audioUrl: "",
      bodyText: "",
      customModalItems: [],
      displayPusherMessage: false,
      images: [],
      imageUrl: "",
      itinerary: "",
      currentModalPosition: "center", // left / right / center
      currentModalSize: "small", // small / medium / large / x-large / "" = full screen
      pusherEnabled: false,
      pusherMessage: "",
      search: "",
      showCustomModal: false,
      showModal: false,
      subTitle: "",
      tableFooter: "",
      tableHeaders: [],
      tableRows: [],
      tableTitle: "",
      tableEnableSearch: false,
      tableRowsPerPage: [5, 10, 25],
      title: "",
      transactionHeaders: [
        {
          align: "left",
          sortable: false,
          text: "Date",
          value: "date",
          width: "20%"
        },
        {
          align: "left",
          text: "Description",
          value: "description",
          width: "65%"
        },
        {
          align: "right",
          sortable: true,
          text: "Amount",
          value: "amount",
          width: "15%"
        }
      ],
      transactionItems: [],
      videoType: "",
      videoUrl: "",
      address: "",
      vimeoVideoId: "",
      youTubeVideoId: ""
    };
  },
  mounted() {
    // if (this.pusherEnabled) {
    //   console.log("Setting up pusher");
    //   this.$pusher.subscribe("web-channel");
    //   console.log("Subscribed to 'web-channel'");
    //   let that = this;
    //   this.$pusher.bind(
    //     "some-event-and-possibly-some-unique-id-for-user",
    //     data => {
    //       console.log("Message from Teneo: " + data.message);
    //       that.pusherMessage = data.message;
    //       that.displayPusherMessage = true;
    //     },
    //     null
    //   );
    // }
  },
  watch: {
    modalItem() {
      if (
        (this.modalItem && this.$store.getters.showModal) ||
        this.itemHasLongResponse(this.modalItem)
      ) {
        this.resetModal();
        let item = this.modalItem;
        let transcript = this.liveChatTranscript(item);
        let displayModal = false;

        // check if user wants to talk to a live agent
        // console.log("Live Chat? :" + this.isLiveChat);
        if (transcript !== "undefined" && this.isLiveChat) {
          this.$store.commit(
            "LIVE_CHAT",
            "======= VIRTUAL ASSISTANT CONVERSATION HISTORY =======\n" +
              transcript +
              "====================================================\n"
          );
          this.$store.commit("HIDE_CHAT_MODAL"); // stops the transcript from being sent back constantly during a live chat
        }
        // send URL's to the I-FRAME
        let outputUrl = this.outputLink(item);
        if (outputUrl !== "" && !this.showButtonOnly) {
          if (outputUrl.startsWith("./")) {
            let currentIframeUrl =
              this.iFrameUrlBase + outputUrl.substring(2, outputUrl.length);
            this.$store.commit("UPDATE_FRAME_URL", currentIframeUrl);
          } else {
            this.$store.commit("UPDATE_FRAME_URL", outputUrl);
          }
        }

        if (this.hasModal(this.modalItem)) {
          let extensions = this.itemExtensionsModal(item);
          extensions.forEach(extension => {
            this.transactionItems = [];
            displayModal = true;

            // check for modal sizes and positions
            if (this.modalSize(item) !== "undefined") {
              this.currentModalSize = this.modalSize(item);
            }

            if (this.modalPosition(item) !== "undefined") {
              this.currentModalPosition = this.modalPosition(item);
            }

            // check for flight itinerary
            if (extension.name === "displayItinerary") {
              this.title = this.getFirstChunk(item.text);
              this.itinerary = extension.parameters;
            }

            // check for displayTranactionTable - myBank
            if (extension.name === "displayTable") {
              if (
                "overrideTitle" in extension.parameters &&
                extension.parameters.overrideTitle
              ) {
                this.title = extension.parameters.title;
              } else {
                this.title = this.getFirstChunk(item.text);
                this.tableTitle = extension.parameters.title;
              }
              this.tableEnableSearch = extension.parameters.enableSearch;
              this.tableRows = extension.parameters.rows;
              this.tableHeaders = extension.parameters.headers;
              this.tableRowsPerPage = extension.parameters.rowsPerPage;
            }

            // check for displayTranactionTable - myBank
            if (extension.name === "displayTransactionsTable") {
              this.title = this.getFirstChunk(item.text);
              this.transactionItems = [];
              extension.parameters.transactions.transactions.forEach(
                transaction => {
                  // console.log(transaction);
                  this.transactionItems.push({
                    date: transaction.Date,
                    description: transaction.Description,
                    amount: transaction.Amount
                  });
                }
              );
            }

            // check for display image action
            if (extension.name === "displayImage") {
              this.title = this.getFirstChunk(item.text);
              this.imageUrl = extension.parameters.image_url;
            }

            // check for display image action
            if (extension.name === "displayImageCarousel") {
              this.title = this.getFirstChunk(item.text);
              this.images = extension.parameters.images;
            }

            // check for basic card action
            if (extension.name === "displayBasicCard") {
              this.title = extension.parameters.title;
              this.bodyText = extension.parameters.content;
            }

            // check for image card action
            if (extension.name === "displayImageCard") {
              this.title = extension.parameters.title;
              this.bodyText = extension.parameters.content;
              this.imageUrl = extension.parameters.image_url;
            }

            // check for panel card action
            if (extension.name === "displayPanelCard") {
              this.title = this.getFirstChunk(item.text);
              this.bodyText = extension.parameters.content;
            }

            // Check for a custom modal layout
            if (extension.name.startsWith("displayModal")) {
              displayModal = false;
              this.showCustomModal = true;
              this.customModalItems = extension.items;
              this.$store.commit("SHOW_CUSTOM_MODAL");
            }

            // check for horizontal card action
            if (extension.name === "displayHorizontalCard") {
              this.imageUrl = extension.parameters.image;
              this.title = extension.parameters.title;
              this.bodyText = extension.parameters.content;
            }

            if (extension.name === "displayMap") {
              this.address = extension.parameters.address;
            }

            // check for display video action
            if (extension.name === "displayVideo") {
              let url = extension.parameters.video_url;
              let videoId = this.youTubeIdFromUrl(url);
              if (!videoId) {
                videoId = this.vimeoIdFromUrl(url);
                // console.log("vimeoid: " + videoId);
                if (videoId) {
                  this.vimeoVideoId = videoId;
                } else {
                  const audioFileExt = this.isAudioFile(url);
                  if (audioFileExt) {
                    this.audioType = `audio/${audioFileExt}`;
                    this.audioUrl = url;
                  } else {
                    const videoFileExt = this.isVideoFile(url);
                    if (videoFileExt) {
                      this.videoType = `video/${videoFileExt}`;
                      this.videoUrl = url;
                    }
                  }
                }
              } else {
                this.youTubeVideoId = videoId;
              }

              this.title = this.getFirstChunk(item.text);
            }
          });
        }
        if (this.itemHasLongResponse(this.modalItem)) {
          this.title = decodeURIComponent(
            this.modalItem.teneoResponse.lastinput
          );
          this.bodyText = this.modalItem.text;
          // this.bodyText = this.modalItem.text.replace(
          //   /(?:\r\n|\r|\n)/g,
          //   "<br/><br/>"
          // );
          displayModal = true;
        }
        // look for anchors with callbacks
        if (this.bodyText) {
          this.bodyText = this.bodyText.replace(
            /(?:onclick='DI\.VA\.hope\.sendInput\(")([^"]+)(?:"\)')/g,
            'data-input="$1" class="sendInput"'
          );
        }
        this.showModal = displayModal;
      } else {
        this.showModal = false;
      }
    }
  },
  computed: {
    ...mapGetters([
      "dark",
      "embed",
      "showButtonOnly",
      "extensionIsInline",
      "hasInline",
      "hasModal",
      "hasInlineType",
      "iFrameUrlBase",
      "itemExtensions",
      "itemExtensionsModal",
      "isAudioFile",
      "isLiveChat",
      "isVideoFile",
      "liveChatTranscript",
      "modalItem",
      "modalSize",
      "modalPosition",
      "outputLink",
      "userInput",
      "vimeoIdFromUrl",
      "youTubeIdFromUrl",
      "itemHasLongResponse"
    ]),
    showPusher() {
      // if (this.displayPusherMessage) {
      //   return true;
      // }
      // return false;
      return false;
    },
    toolbarWidth() {
      // console.log("Seeing if we need to adjust toolbar style");
      if (this.currentModalSize !== "") {
        // console.log("Yep adjusted toolbar style");
        return `teneo-modal-${this.currentModalSize}-width`;
      }
      return "";
    }
  },
  updated() {
    this.modalClass();
    if (this.bodyText) {
      let chatModalDiv = document.getElementById("chat-modal-html");
      if (chatModalDiv) {
        chatModalDiv.addEventListener("click", this.onHtmlClickInModal);
      }
    }
  },
  methods: {
    getFirstChunk(text) {
      if (text && text.includes("||")) {
        return text.split("||")[0];
      }
      return text;
    },
    modalClass() {
      // console.log("Applying custom modal size and position");
      // console.log("Adding sizing and position styles to modal");
      var modalElements = document.getElementsByClassName("teneo-modal");
      if (
        modalElements !== "undefined" &&
        this.currentModalSize !== "undefined"
      ) {
        for (var i = 0; i < modalElements.length; i++) {
          modalElements[
            i
          ].className += ` teneo-modal-${this.currentModalPosition} teneo-modal-${this.currentModalSize}-width`;
        }
      }
    },
    removeCustomStylesFromModal() {
      // console.log("removing custom styles from modal");
      var modalElements = document.getElementsByClassName("teneo-modal");
      if (modalElements !== "undefined") {
        for (var i = 0; i < modalElements.length; i++) {
          // console.log("Removing existing modal styles - reset");
          modalElements[i].classList.remove("teneo-modal-center");
          modalElements[i].classList.remove("teneo-modal-right");
          modalElements[i].classList.remove("teneo-modal-left");
          modalElements[i].classList.remove("teneo-modal-small-width");
          modalElements[i].classList.remove("teneo-modal-medium-width");
          modalElements[i].classList.remove("teneo-modal-large-width");
          modalElements[i].classList.remove("teneo-modal-x-large-width");
        }
      }
    },
    onHtmlClickInModal(event) {
      // console.log("html link clicked in modal");
      // Find the closest anchor to the target.
      const anchor = event.target.closest("a");
      if (!anchor) return;

      // Check to make sure this is from our v-html because
      // we don't want to handle clicks from other things in
      // the Vue
      if (
        !anchor.classList.contains("sendInput") &&
        !anchor.classList.contains("openInIframe")
      ) {
        return; // basically treat like a normal link
      } else if (anchor.classList.contains("openInIframe")) {
        // open in iframe
        event.stopPropagation();
        event.preventDefault();
        this.$store.commit("UPDATE_FRAME_URL", anchor.getAttribute("href"));
      } else {
        event.stopPropagation();
        event.preventDefault();
        if (anchor.getAttribute("data-input")) {
          this.updateInputBox(anchor.getAttribute("data-input"));
        } else {
          this.updateInputBox(anchor.innerText);
        }
        this.sendUserInput("&isClick=true");
      }
    },
    sendUserInput(params = "") {
      if (this.userInput) {
        this.$store.commit("SHOW_PROGRESS_BAR");
        this.$store.dispatch("sendUserInput", params);
      }
    },
    updateInputBox(userInput) {
      this.$store.commit("SET_USER_INPUT", userInput);
    },
    hideModal() {
      this.$store.commit("HIDE_CHAT_MODAL");
      let that = this;
      setTimeout(function() {
        that.resetModal();
      }, 1000); // needed to stop weird animations on the close
    },
    resetModal() {
      this.actions = "";
      this.audioType = "";
      this.audioUrl = "";
      this.bodyText = "";
      this.customModalItems = [];
      this.imageUrl = "";
      this.images = [];
      this.itinerary = "";
      this.currentModalPosition = "center";
      this.currentModalSize = "small";
      this.removeCustomStylesFromModal();
      this.search = "";
      this.showCustomModal = false;
      this.showModal = false;
      this.subTitle = "";
      this.tableFooter = "";
      this.tableHeaders = [];
      this.tableRows = [];
      this.tableTitle = "";
      this.tableEnableSearch = false;
      this.tableRowsPerPage = [5, 10, 25];
      this.title = "";
      this.transactionItems = [];
      this.videoType = "";
      this.videoUrl = "";
      this.address = "";
      this.vimeoVideoId = "";
      this.youTubeVideoId = "";
    }
  }
};
</script>

<style>
.v-toolbar--fixed {
  left: unset !important;
  z-index: 5000;
}

.modal-container {
  padding: 0px;
  /* margin-top: 20px; */
}

.modal-height {
  min-height: calc(100vh - 64px) !important;
  height: fit-content;
}

.v-menu__content {
  position: inherit !important;
}

.cardText {
  padding-top: 5px;
  text-align: left;
}

.modal-headline {
  font-size: 0.7em;
}

.cardText table {
  border-collapse: collapse;
  text-align: left;
  width: 100%;
  border: 3px solid rgb(105, 104, 104);
}

.cardText table td,
.cardText table th {
  padding: 3px 10px;
}

.cardText table thead th {
  background-color: #8e8e93b5;
  font-size: 11px;
  font-weight: 700;
  border-left: 1px solid #333;
}

.cardText table td.summaryHeader {
  border-top: 1px solid #8e8e93;
  border-bottom: 1px solid #8e8e93;
  background-color: #fafafa;
}

.cardText table tbody td {
  border-left: 1px solid #8e8e93;
  font-size: 12px;
  font-weight: 400;
}

.cardText table tbody td:first-child {
  border-left: none;
}

.cardText table tbody tr:last-child td {
  border-bottom: none;
}

.teneo-modal {
  position: absolute !important;
  right: 0 !important;
  margin: 0 !important;
  /* padding-top: 48px !important; */
  overflow-y: auto;
  overflow-x: hidden;
}

.teneo-modal-card {
  overflow-y: auto;
  overflow-x: hidden;
  height: auto;
}

.teneo-modal-center {
  margin-right: auto !important;
  margin-left: auto !important;
  right: unset !important;
  position: unset !important;
}

.teneo-modal-right {
  left: unset !important;
  right: 0px !important;
}

.teneo-modal-left {
  right: unset !important;
  left: 0px !important;
}

.teneo-modal-small-width {
  max-width: 360px !important;
}

.teneo-modal-medium-width {
  max-width: 500px !important;
}

.teneo-modal-large-width {
  max-width: 700px !important;
}

.teneo-modal-x-large-width {
  max-width: 900px !important;
}

@media only screen and (max-width: 480px) {
  .modal-fly-out {
    width: 100vw !important;
  }

  .teneo-modal,
  .teneo-modal-small-width,
  .teneo-modal-medium-width,
  .teneo-modal-large-width,
  .teneo-modal-x-large-width {
    max-width: unset !important;
  }
}
</style>