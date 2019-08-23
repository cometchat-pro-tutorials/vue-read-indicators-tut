<template>
  <div class="booker">
    <nav-bar :name="this.username" :avatar="this.avatar" />
    <div class="chat">
        <div class="container">
          <div class="msg-header">
              <div class="active">
                  <h5>#General
                <span class="badge badge-danger" v-if="unreadCount">
                  {{ unreadCount }}
                </span>
              </h5>
              </div>
          </div>
          <div class="chat-page">
              <div class="msg-inbox">
                  <div class="chats" id="chats">
                      <div class="msg-page" id="msg-page">
                        <div
                          v-if="loadingMessages"
                          class="loading-messages-container"
                        >
                          <spinner :size="100"/>
                          <span class="loading-text">
                            Loading Messages
                          </span>
                        </div>
                        <div class="text-center img-fluid empty-chat" v-else-if="!groupMessages.length" >
                          <div class="empty-chat-holder">
                            <img src="../assets/empty-state.svg" class="img-res" alt="empty chat image">
                          </div>
                          <div>
                            <h2> No new message? </h2>
                            <h6 class="empty-chat-sub-title">
                              Send your first message below.
                            </h6>
                          </div>
                        </div>
                        <div v-else>
                          <div v-for="message in groupMessages" v-bind:key="message.id">
                            <div class="received-chats" v-if="message.sender.uid != uid">
                                <div class="received-chats-img">
                                  <img v-bind:src="message.sender.avatar" alt="" class="avatar">
                                </div>
                                <div class="received-msg">
                                    <div class="received-msg-inbox" style="position: relative">
                                        <p><span>{{ message.sender.uid }}</span><br>{{ message.data.text }}</p>
                                    </div>
                                </div>
                              </div>

                            <div class="outgoing-chats" v-else>
                                  <div class="outgoing-chats-msg" style="position: relative;">
                                      <p>{{ message.data.text }}</p>

                                          <div v-if="message.delivered" style="position: absolute; right: 0; bottom: 0;padding: 10px 10px 0 0;">
                                         <svg xmlns="http://www.w3.org/2000/svg" x="0px" y="0px" width="23" height="23" viewBox="0 0 226 226" style=" fill:#000000;">
                                            <g fill="none" fill-rule="nonzero" stroke="none" stroke-width="1" stroke-linecap="butt" stroke-linejoin="miter" stroke-miterlimit="10" stroke-dasharray
                                              stroke-dashoffset="0" font-family="none" font-weight="none" font-size="none" text-anchor="none" style="mix-blend-mode: normal">
                                              <path d="M0,226v-226h226v226z" fill="none" />
                                              <g fill="#2ecc71">
                                                <path d="M212.68483,42.375l-109.1015,109.90192l-43.17071,-43.98525l-13.32929,13.43758l56.5,57.18742l122.41667,-123.396z"/>
                                                <path d="M165.6015,42.375l-109.1015,109.90192l-43.17071,-43.98525l-13.32929,13.43758l56.5,57.18742l122.41667,-123.396z"/>
                                              </g>
                                            </g>
                                          </svg>
                                      </div>

                                      <div v-tooltip="{content: readers,loadingContent: '<i>Loading...</i>',}" v-else-if="message.read" style="position: absolute; right: 0; bottom: 0;padding: 10px 10px 0 0;">
                                         <svg xmlns="http://www.w3.org/2000/svg" x="0px" y="0px" width="23" height="23" viewBox="0 0 226 226" style=" fill:#000000;">
                                          <g fill="none" fill-rule="nonzero" stroke="none" stroke-width="1" stroke-linecap="butt" stroke-linejoin="miter" stroke-miterlimit="10" stroke-dasharray
                                            stroke-dashoffset="0" font-family="none" font-weight="none" font-size="none" text-anchor="none" style="mix-blend-mode: normal">
                                            <path d="M0,226v-226h226v226z" fill="none" />
                                            <g>
                                              <path d="M212.68483,42.375l-109.1015,109.90192l-43.17071,-43.98525l-13.32929,13.43758l56.5,57.18742l122.41667,-123.396z" fill="#0c9eff"/>
                                              <path d="M165.6015,42.375l-109.1015,109.90192l-43.17071,-43.98525l-13.32929,13.43758l56.5,57.18742l122.41667,-123.396z" fill="#06a3ff"/>
                                            </g>
                                          </g>
                                        </svg>
                                      </div>
                                  </div>
                                  <div class="outgoing-chats-img">
                                      <img v-bind:src="message.sender.avatar" alt="" class="avatar">
                                  </div>
                              </div>
                          </div>
                        </div>
                      </div>
                  </div>
              </div>

              <div class="msg-bottom">
                <form class="message-form" v-on:submit.prevent="sendGroupMessage">
                  <div class="input-group">
                    <input type="text" class="form-control message-input" placeholder="Type something" v-model="chatMessage" required>
                    <spinner
                      v-if="sendingMessage"
                      class="sending-message-spinner"
                      :size="30"
                    />
                  </div>
                </form>
              </div>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
import { CometChat } from "@cometchat-pro/chat";
import NavBar from "../components/NavBar.vue";
import Spinner from "../components/Spinner.vue";
export default {
  name: "home",
  components: {
    NavBar,
    Spinner
  },
  data() {
    return {
      username: "",
      avatar: "",
      uid: "",
      messageId: "",
      sendingMessage: false,
      chatMessage: "",
      loggingOut: false,
      groupMessages: [],
      loadingMessages: false,
      unreadCount: 0,
      readers: [],
    };
  },

  mounted() {  
    this.loadingMessages = true;
    var listenerID = "UNIQUE_LISTENER_ID";
    const messagesRequest = new CometChat.MessagesRequestBuilder()
      .setLimit(100)
      .build();
    messagesRequest.fetchPrevious().then(
      messages => {
        console.log("Message list fetched:", messages);

        this.groupMessages = messages;
        this.loadingMessages = false;


        this.$nextTick(() => {
          this.scrollToBottom();
        });
      },
      error => {
        console.log("Message fetching failed with error:", error);
      }
    );
    CometChat.addMessageListener(
      listenerID,
      new CometChat.MessageListener({
        onTextMessageReceived: textMessage => {
          console.log("Text message received successfully", textMessage);

          this.groupMessages = [...this.groupMessages, textMessage];

          if (document.hasFocus()) {
            this.markMessageAsRead(textMessage);
          }
          this.updateUnreadMessages();

          this.loadingMessages = false;
          this.$nextTick(() => {
            this.scrollToBottom();
          });
        },
        onMessageDelivered: messageReceipt => {
          console.log("Message has been delivered ", messageReceipt);

          const content = this.groupMessages.map(message => {
            if (message.id === messageReceipt.messageId) {
              return {...message, delivered: true, read: false}
            }
            return message;
          });
          this.groupMessages = content;
        },
        onMessageRead: messageReceipt => {
          console.log("Message read", messageReceipt);

          const updatedContent = this.groupMessages.map(message => {
            if (message.id === messageReceipt.messageId) {
              return {...message, read: true, delivered: false}
            }
            return message;
          });
          this.groupMessages = updatedContent;

          this.updateMessageReaders(messageReceipt);
        }
      })
    );
    this.handleFocus();
        this.updateUnreadMessages();

  },

  created() {
    this.getLoggedInUser();
  },
  methods: {
    updateUnreadMessages() {
      CometChat.getUnreadMessageCountForGroup("supergroup").then(
        data => {
          console.log("Message count fetched for unread", data);
        
          data.supergroup > 0 ? this.unreadCount = data.supergroup : this.unreadCount = 0 ;
        },
        error => {
          console.log("Error in getting message count", error);
        }
      );
    },
    updateMessageReaders(messageReceipt) {
      let messageReaders = [];
      CometChat.getMessageReceipts(messageReceipt.messageId).then(receipts => {
      console.log("Message details receipt fetched:", receipts);
                
        receipts.forEach(data => {
          messageReaders = [...messageReaders, data.sender.uid]
        });
        this.readers = [...new Set(messageReaders)];
                  
      }, error => {
        console.log("Error in getting messag details ", error)
      });
    },
    handleFocus() {
      window.addEventListener("focus", () => {
        const updatedMessages = this.groupMessages.map(message => {
          if (!message.markedAsRead && message.sender.uid !== this.uid) {
            this.markMessageAsRead(message);
            return {...message, markedAsRead: true}
          }
          return message;
        });
        this.groupMessages = updatedMessages;
        this.updateUnreadMessages();
      });
    },
    getLoggedInUser() {
      CometChat.getLoggedinUser().then(
        user => {
          this.username = user.name;
          this.avatar = user.avatar;
          this.uid = user.uid;
        },
        error => {
          this.$router.push({
            name: "homepage"
          });
          console.log(error);
        }
      );
    },

    markMessageAsRead(textMessage) {
      CometChat.markMessageAsRead(textMessage);
    },

    sendGroupMessage() {
      this.sendingMessage = true;
      let receiverID = process.env.VUE_APP_COMMETCHAT_GUID;
      let messageText = this.chatMessage;
      let messageType = CometChat.MESSAGE_TYPE.TEXT;
      let receiverType = CometChat.RECEIVER_TYPE.GROUP;
      let globalContext = this;
      let textMessage = new CometChat.TextMessage(
        receiverID,
        messageText,
        messageType,
        receiverType
      );
      CometChat.sendMessage(textMessage).then(
        message => {
          console.log("Message sent successfully:", message);
          this.chatMessage = "";
          this.sendingMessage = false;
          this.groupMessages = [...globalContext.groupMessages, message];
          this.$nextTick(() => {
            this.scrollToBottom()
          })
        },
        error => {
          console.log("Message sending failed with error:", error);
        }
      );
    },
    scrollToBottom() {
      const chat = document.getElementById("msg-page");
      chat.scrollTo(0, chat.scrollHeight + 30);
   }
  }
};
</script>

<style lang="scss">
.tooltip {
  display: block !important;
  z-index: 10000;

  .tooltip-inner {
    background: black;
    color: white;
    border-radius: 16px;
    padding: 5px 10px 4px;
  }

  .tooltip-arrow {
    width: 0;
    height: 0;
    border-style: solid;
    position: absolute;
    margin: 5px;
    border-color: black;
    z-index: 1;
  }

  &[x-placement^="top"] {
    margin-bottom: 5px;

    .tooltip-arrow {
      border-width: 5px 5px 0 5px;
      border-left-color: transparent !important;
      border-right-color: transparent !important;
      border-bottom-color: transparent !important;
      bottom: -5px;
      left: calc(50% - 5px);
      margin-top: 0;
      margin-bottom: 0;
    }
  }

  &[x-placement^="bottom"] {
    margin-top: 5px;

    .tooltip-arrow {
      border-width: 0 5px 5px 5px;
      border-left-color: transparent !important;
      border-right-color: transparent !important;
      border-top-color: transparent !important;
      top: -5px;
      left: calc(50% - 5px);
      margin-top: 0;
      margin-bottom: 0;
    }
  }

  &[x-placement^="right"] {
    margin-left: 5px;

    .tooltip-arrow {
      border-width: 5px 5px 5px 0;
      border-left-color: transparent !important;
      border-top-color: transparent !important;
      border-bottom-color: transparent !important;
      left: -5px;
      top: calc(50% - 5px);
      margin-left: 0;
      margin-right: 0;
    }
  }

  &[x-placement^="left"] {
    margin-right: 5px;

    .tooltip-arrow {
      border-width: 5px 0 5px 5px;
      border-top-color: transparent !important;
      border-right-color: transparent !important;
      border-bottom-color: transparent !important;
      right: -5px;
      top: calc(50% - 5px);
      margin-left: 0;
      margin-right: 0;
    }
  }

  &[aria-hidden='true'] {
    visibility: hidden;
    opacity: 0;
    transition: opacity .15s, visibility .15s;
  }

  &[aria-hidden='false'] {
    visibility: visible;
    opacity: 1;
    transition: opacity .15s;
  }
}
</style>
