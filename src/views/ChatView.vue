<template>
  <div class="container-fluid h-100">
    <div class="row clearfix h-100">
      <div class="col-lg-12 h-100">
        <div class="card chat-app h-100">
          <div id="plist" class="people-list">
            <h1>Live Chat</h1>
            <hr />
            <ul class="list-unstyled chat-list mt-2 mb-0">
              <!-- <h3>Group Chats</h3>-->
              <h3>Rooms</h3>
              <li
                v-for="chat in chats"
                class="clearfix"
                :key="chat"
                :class="Number(activeChat) == Number(chat.id) ? 'active' : ''"
                @click="openChat(chat.id)"
              >
                <img :src="chat.img" alt="avatar" />
                <div class="about">
                  <div class="name">
                    <strong>{{ chat.name }}</strong>
                  </div>
                  <div class="status">
                    <i
                      class="fa fa-circle"
                      :class="
                        chat.description == 'With AI' ? 'online' : 'offline'
                      "
                    ></i>
                    {{ chat.description
                    }}<!--left 7 mins ago-->
                  </div>
                </div>
              </li>
              <!--Comming in an update-->
              <!--<h3>Direct Messages</h3>

              <li class="clearfix active">
                <img
                  src="https://alex.polan.sk/livechat/avatar/avatar2.png"
                  alt="avatar"
                />
                <div class="about">
                  <div class="name">Aiden Chavez</div>
                  <div class="status">
                    <i class="fa fa-circle online"></i> online
                  </div>
                </div>
              </li>-->
            </ul>
          </div>
          <div class="chat">
            <div class="chat-header clearfix">
              <div class="row">
                <div class="col-lg-6">
                  <a>
                    <img :src="currentChat.img" alt="avatar" />
                  </a>
                  <div class="chat-about">
                    <h6 class="mb-0">{{ currentChat.name }}</h6>
                    <small
                      ><i class="fa fa-circle online"></i>
                      {{ onlineCount }} people online</small
                    >
                  </div>
                </div>
                <div class="col-lg-6 text-end">
                  <!--   <a
                    href="javascript:void(0);"
                    class="btn btn-outline-secondary"
                    ><i class="fa fa-camera"></i
                  ></a>
                  <a href="javascript:void(0);" class="btn btn-outline-primary"
                    ><i class="fa fa-image"></i
                  ></a>
                  <a href="javascript:void(0);" class="btn btn-outline-info"
                    ><i class="fa fa-cogs"></i
                  ></a>
                  <a href="javascript:void(0);" class="btn btn-outline-warning"
                    ><i class="fa fa-question"></i
                  ></a>-->
                  <!--Comming in an Update (maybe)-->
                  <!-- <div class="dropdown">
                    <button
                      class="btn btn-secondary dropdown-toggle"
                      type="button"
                      id="dropdownMenuButton"
                      data-bs-toggle="dropdown"
                      aria-expanded="false"
                    >
                      <i class="fa fa-ellipsis-v"></i>
                    </button>
                    <ul
                      class="dropdown-menu dropdown-menu-end"
                      aria-labelledby="dropdownMenuButton"
                    >
                      <li><a class="dropdown-item" href="#">Action 1</a></li>
                      <li><a class="dropdown-item" href="#">Action 2</a></li>
                      <li><a class="dropdown-item" href="#">Action 3</a></li>
                    </ul>
                  </div>
                  -->
                </div>
              </div>
            </div>
            <div class="chat-history">
              <ul class="mb-0" ref="messagesContainer">
                <!--<li class="clearfix">
                  <div class="message-data text-end">
                    <span class="message-data-time">10:10 AM, Today</span>
                    <img
                      src="https://alex.polan.sk/livechat/avatar/avatar7.png"
                      alt="avatar"
                    />
                  </div>
                  <div class="message other-message float-end">
                    Hi Aiden, how are you? How is the project coming along?
                  </div>
                </li>
                <li class="clearfix">
                  <div class="message-data">
                    <img
                      src="https://alex.polan.sk/livechat/avatar/avatar7.png"
                      alt="avatar"
                    />
                    <span class="message-data-time">10:12 AM, Today</span>
                  </div>
                  <div class="message my-message">Are we meeting today?</div>
                </li>-->

                <li
                  v-for="message in messages"
                  class="clearfix"
                  :key="message.id"
                >
                  <div
                    class="message-data"
                    :class="message.author == username ? 'text-end' : ''"
                  >
                    <img
                      v-if="message.author != username"
                      :src="
                        'https://alex.polan.sk/livechat/avatar/' +
                        message.avatar
                      "
                      alt="avatar"
                    />
                    <span class="message-data-time"
                      ><strong>{{
                        // formatTimestamp(message.timestamp)
                        message.author
                      }}</strong></span
                    >
                    <img
                      v-if="message.author == username"
                      :src="
                        'https://alex.polan.sk/livechat/avatar/' +
                        message.avatar
                      "
                      alt="avatar"
                    />
                  </div>
                  <div
                    class="message"
                    :class="
                      message.author == username
                        ? 'other-message float-end'
                        : 'my-message'
                    "
                  >
                    {{ message.message }}
                  </div>
                </li>
                <div class="new-messages" v-if="newMessages">
                  <button @click="scrollToBottom">New Messages ↓</button>
                </div>
              </ul>
            </div>
            <div class="chat-message clearfix sticky-bottom">
              <div class="input-group mb-0">
                <input
                  type="text"
                  class="form-control"
                  placeholder="Enter text here..."
                  v-model="newMessage"
                  @keyup.enter="sendMessage"
                />
                <div class="input-group-prepend">
                  <span @click="sendMessage" class="input-group-text"
                    ><i class="fa fa-paper-plane"></i
                  ></span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      lastClientHeight: 0,
      messages: [],
      old_messages: [],
      newMessage: "",
      username: localStorage.getItem("username") ?? "User",
      ticker1: 1,
      ticker2: 1,
      newMessages: false,
      activeChat: 1,
      /* chats: [
        {
          id: 1,
          img: "https://alex.polan.sk/livechat/avatar/avatar1.png",
          name: "Vincent Porter",
          description: "w/ AI",
        },
        {
          id: 2,
          img: "https://alex.polan.sk/livechat/avatar/avatar2.png",
          name: "Aiden Chavez",
          description: "w/ AI",
        },
        {
          id: 3,
          img: "https://alex.polan.sk/livechat/avatar/avatar3.png",
          name: "Mike Thomas",
          description: "w/ AI",
        },
        {
          id: 4,
          img: "https://alex.polan.sk/livechat/avatar/avatar4.png",
          name: "Christian Kelly",
          description: "w/ AI",
        },
        {
          id: 5,
          img: "https://alex.polan.sk/livechat/avatar/avatar5.png",
          name: "Monica Ward",
          description: "no AI",
        },
        {
          id: 6,
          img: "https://alex.polan.sk/livechat/avatar/avatar6.png",
          name: "Dean Henry",
          description: "no AI",
        },
      ],*/
      chats: [
        {
          id: 1,
          img: "https://alex.polan.sk/livechat/room/room1.jpg",
          name: "AI Room 1",
          description: "With AI",
        },
        {
          id: 2,
          img: "https://alex.polan.sk/livechat/room/room2.jpg",
          name: "AI Room 2",
          description: "With AI",
        },
        {
          id: 3,
          img: "https://alex.polan.sk/livechat/room/room3.jpeg",
          name: "AI Room 3",
          description: "With AI",
        },
        {
          id: 4,
          img: "https://alex.polan.sk/livechat/room/room4.jpeg",
          name: "AI Room 4",
          description: "With AI",
        },
        {
          id: 5,
          img: "https://alex.polan.sk/livechat/room/room5.webp",
          name: "Real Pirates Room 1",
          description: "Without AI",
        },
        {
          id: 6,
          img: "https://alex.polan.sk/livechat/room/room6.jpeg",
          name: "Real Pirates Room 2",
          description: "Without AI",
        },
        {
          id: 7,
          img: "https://alex.polan.sk/livechat/room/room7.png",
          name: "Skibidi Toilet Room",
          description: "Without AI",
        },
        {
          id: 8,
          img: "https://alex.polan.sk/livechat/room/room8.webp",
          name: "SpongeBob Room",
          description: "Without AI",
        },
      ],
      onlineCount: 0,
      currentChat: {},
    };
  },
  created() {
    const storedUsername = localStorage.getItem("username");
    const verificationId = localStorage.getItem("verification_id");
    if (!storedUsername || !verificationId) {
      this.$router.push("/");
    }
    this.setCurrentChat();
    this.fetchMessages();
    setInterval(() => {
      this.fetchMessages();
    }, 1000);
  },
  beforeUnmount() {
    window.removeEventListener("resize", this.updateHeadingText);
  },
  watch: {
    "$route.params.roomId": function (newVal, oldVal) {
      if (newVal !== oldVal) {
        this.ticker2 = 1;
        this.setCurrentChat();
      }
    },
  },
  methods: {
    setCurrentChat() {
      const chatId = parseInt(this.$route.params.roomId);
      this.currentChat = this.chats.find((chat) => chat.id === chatId) || {};
      console.log(this.currentChat);
    },
    formatTimestamp(timestamp) {
      const date = new Date(timestamp);
      const now = new Date();
      const options = { hour: "2-digit", minute: "2-digit", hour12: true };

      const isToday = date.toDateString() === now.toDateString();
      const isYesterday =
        date.toDateString() ===
        new Date(now.setDate(now.getDate() - 1)).toDateString();

      if (isToday) {
        return `Today, ${date.toLocaleTimeString("en-US", options)}`;
      } else if (isYesterday) {
        return `Yesterday, ${date.toLocaleTimeString("en-US", options)}`;
      } else {
        return `${date.toLocaleDateString("en-US")} ${date.toLocaleTimeString(
          "en-US",
          options
        )}`;
      }
    },
    fetchMessages(scroll = true) {
      const roomId = this.$route.params.roomId;
      this.activeChat = roomId;
      this.$axios
        .get(
          "livechat.php?room_id=" +
            roomId +
            "&verification_id=" +
            localStorage.getItem("verification_id")
        )
        .then((response) => {
          this.old_messages = this.messages;
          this.messages = response.data.messages;
          this.onlineCount = response.data.online_count;
        })
        .then(() => {
          const container = this.$refs.messagesContainer;

          if (this.ticker2 == 1 || !scroll) {
            this.scrollToBottom();
            this.ticker2++;
          } else if (
            this.old_messages.length != this.messages.length &&
            scroll &&
            container.scrollHeight > container.clientHeight
          ) {
            if (this.lastClientHeight == container.scrollTop) {
              this.scrollToBottom();
            } else {
              this.newMessages = true;
            }
          }
        })
        .catch((error) => {
          console.error("Error fetching messages:", error);
        });
    },
    scrollToBottom() {
      console.log("scrolling to bottom");
      const container = this.$refs.messagesContainer;
      container.scrollTop = container.scrollHeight;
      this.handleScroll();
      this.lastClientHeight = container.scrollTop;
    },
    handleScroll() {
      const container = this.$refs.messagesContainer;
      if (
        container.scrollTop + container.clientHeight >=
        container.scrollHeight - 2
      ) {
        this.newMessages = false;
      }
    },
    sendMessage() {
      if (this.newMessage.trim() !== "") {
        const roomId = this.$route.params.roomId;
        const message = {
          author: localStorage.getItem("username") ?? "User",
          message: this.newMessage,
          type: "user",
          room_id: roomId,
          verification_id: localStorage.getItem("verification_id"),
        };
        this.$axios
          .post("livechat.php", message)
          .then((response) => {
            if (response.data.status === "success") {
              this.fetchMessages(false);
              const container = this.$refs.messagesContainer;
              container.scrollTop = container.scrollHeight;
              this.newMessage = "";
            } else if (response.data.status === "error") {
              alert(
                "Message could not be send!\nError: " + response.data.message
              );
            }
          })
          .catch((error) => {
            console.error("Error sending message:", error);
          });
      }
    },
    openChat(id) {
      this.activeChat = id;
      this.$router.push("/room/" + id);
    },
  },
};
</script>

<style scoped>
body {
  background-color: #f4f7f6;
  margin-top: 20px;
}
.card {
  background: #fff;
  transition: 0.5s;
  border: 0;
  margin-bottom: 30px;
  position: relative;
  width: 100%;
  height: 100%;
  border-radius: 0;
  box-shadow: 0 1px 2px 0 rgb(0 0 0 / 10%);
}
.chat-app .people-list {
  width: 280px;
  position: absolute;
  left: 0;
  top: 0;
  padding: 20px;
  z-index: 7;
}

.chat-app .chat {
  margin-left: 280px;
  border-left: 1px solid #eaeaea;
}

.people-list {
  transition: 0.5s;
}

.people-list .chat-list li {
  padding: 10px 15px;
  list-style: none;
}

.people-list .chat-list li:hover {
  background: #efefef;
  cursor: pointer;
}

.people-list .chat-list li.active {
  background: #efefef;
}

.people-list .chat-list li .name {
  font-size: 15px;
}

.people-list .chat-list img {
  width: 45px;
  border-radius: 50%;

  /*NEW*/
  aspect-ratio: 1/1;
  object-fit: cover;
  /* -- */
}

.people-list img {
  float: left;
  border-radius: 50%;
}

.people-list .about {
  float: left;
  padding-left: 8px;
}

.people-list .status {
  color: #999;
  font-size: 13px;
}

.chat .chat-header {
  padding: 15px 20px;
  border-bottom: 2px solid #f4f7f6;
}

.chat .chat-header img {
  float: left;
  border-radius: 40px;
  width: 40px;
  /*NEW*/
  aspect-ratio: 1/1;
  object-fit: cover;
  /* -- */
}

.chat .chat-header .chat-about {
  float: left;
  padding-left: 10px;
}

.chat .chat-history {
  padding: 20px;
  border-bottom: 2px solid #fff;
}
.chat-history {
  height: 100%;
}

.chat {
  overflow: hidden;
}
.chat-history > ul {
  height: 85%;
  overflow-y: scroll;
  padding-bottom: 20px !important;
}

.chat .chat-history ul {
  padding: 0;
}

.chat .chat-history ul li {
  list-style: none;
  margin-bottom: 30px;
}

.chat .chat-history ul li:last-child {
  margin-bottom: 0px;
}

.chat .chat-history .message-data {
  margin-bottom: 5px;
}

.chat .chat-history .message-data img {
  /*here*/
  border-radius: 40px;
  width: 28px;
}

.chat .chat-history .message-data-time {
  color: #434651;
  padding-left: 6px;
  padding-right: 6px;
}

.chat .chat-history .message {
  color: #444;
  padding: 14px 20px;
  line-height: 26px;
  font-size: 16px;
  border-radius: 7px;
  display: inline-block;
  position: relative;
}

.chat .chat-history .my-message {
  background: #efefef;
  border-radius: 28px 28px 28px 0 !important;
}

.chat .chat-history .other-message {
  background: #e8f1f3;
  text-align: right;
  border-radius: 28px 28px 0 28px !important;
}

.chat .chat-history .other-message:after {
  border-bottom-color: #e8f1f3;
  left: 93%;
}

.chat .chat-message {
  padding: 20px;
}

.chat-message.sticky-bottom {
  position: sticky;
  bottom: 0;
  background: #fff;
  z-index: 10;
}

.online,
.offline,
.me {
  margin-right: 2px;
  font-size: 8px;
  vertical-align: middle;
}

.online {
  color: #86c541;
}

.offline {
  color: #e47297;
}

.me {
  color: #1d8ecd;
}

.float-end {
  float: right;
}

.clearfix:after {
  visibility: hidden;
  display: block;
  font-size: 0;
  content: " ";
  clear: both;
  height: 0;
}

@media only screen and (max-width: 767px) {
  .chat-app .people-list {
    height: 465px;
    width: 100%;
    overflow-x: auto;
    background: #fff;
    left: -400px;
    display: none;
  }
  .chat-app .people-list.open {
    left: 0;
  }
  .chat-app .chat {
    margin: 0;
  }
  .chat-app .chat-history {
    height: 300px;
    overflow-x: auto;
  }
}

@media only screen and (min-width: 768px) and (max-width: 992px) {
  .chat-app .chat-list {
    height: 650px;
    overflow-x: auto;
  }
  .chat-app .chat-history {
    height: 600px;
    overflow-x: auto;
  }
}

@media only screen and (min-device-width: 768px) and (max-device-width: 1024px) and (orientation: landscape) and (-webkit-min-device-pixel-ratio: 1) {
  .chat-app .chat-list {
    height: 480px;
    overflow-x: auto;
  }
  .chat-app .chat-history {
    height: calc(100vh - 350px);
    overflow-x: auto;
  }
}

.col-lg-12 {
  padding: 0;
  border-radius: 0;
}

.container-fluid,
.container-fluid > .row,
.chat {
  height: 100%;
}

h1 {
  text-align: center;
  margin-bottom: 0 !important;
  font-family: "0Enchanted_Land", cursive;
  font-weight: 900;
}

hr {
  margin-top: 0.25rem !important;
}

.dropdown-menu {
  position: absolute;
  top: 50px;
  right: 10px;
  background-color: white;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  z-index: 1000;
}

.dropdown-item {
  padding: 10px 20px;
  cursor: pointer;
}

.dropdown-item:hover {
  background-color: #f1f1f1;
}

.input-group-text {
  height: 100%;
  margin-left: 5px;
}

.new-messages {
  position: absolute;
  bottom: 80px;
  left: 280px;
  right: 0;
  z-index: 1100;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
