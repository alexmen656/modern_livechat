<template>
  <div class="start-view">
    <div class="overlay"></div>
    <div class="content">
      <h1>Pirate Live Chat</h1>
      <input v-model="username" placeholder="Enter your username" />
      <div class="avatar-selection">
        <h3>Select Your Avatar</h3>
        <div class="avatars">
          <img
            v-for="(avatar, index) in avatars"
            :key="index"
            :src="'https://alex.polan.sk/livechat/avatar/'+avatar"
            :alt="'Avatar ' + (index + 1)"
            :class="{ selected: selectedAvatar === avatar }"
            @click="selectAvatar(avatar)"
          />
        </div>
      </div>
      <button @click="startChat">Start</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      username: "",
      avatars: [
        'avatar1.png',
        'avatar2.png',
        'avatar3.png',
        'avatar4.png',
        'avatar5.png',
        'avatar6.png',
        'avatar8.png'
      ],
      selectedAvatar: null
    };
  },
  created() {
    const storedUsername = localStorage.getItem("username");
    const verificationId = localStorage.getItem("verification_id");
    if (storedUsername && verificationId) {
      this.$router.push("/room/1/");
    }
  },
  methods: {
    selectAvatar(avatar) {
      this.selectedAvatar = avatar;
    },
    async startChat() {
      if (this.username.trim() !== "" && this.selectedAvatar) {
        try {
          const response = await this.$axios.post("register.php", {
            username: this.username,
            avatar: this.selectedAvatar
          });
          if (response.data.status === "success") {
            localStorage.setItem("username", this.username);
            localStorage.setItem("selectedAvatar", this.selectedAvatar);
            localStorage.setItem("verification_id", response.data.verification_id);
            this.$router.push("/room/1/");
          } else {
            alert("Error: " + response.data.message);
          }
        } catch (error) {
          console.error("Error starting chat:", error);
        }
      } else {
        alert("Please enter a username and select an avatar.");
      }
    }
  }
};
</script>

<style scoped>
.start-view {
  position: relative;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #f4f7f6;
  background: url("@/bg.png") no-repeat center center fixed;
  background-size: cover;
}

.overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
}

.content {
  position: relative;
  z-index: 1;
  background: white;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  text-align: center;
}

input {
  padding: 10px;
  font-size: 16px;
  margin-bottom: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
  width: 100%;
  display: block;
  box-sizing: border-box;
}

button {
  padding: 10px 20px;
  font-size: 16px;
  border: none;
  border-radius: 5px;
  background-color: #007bff;
  color: white;
  cursor: pointer;
  width: 100%;
}

button:hover {
  background-color: #0056b3;
}

.avatar-selection {
  text-align: center;
  margin-bottom: 20px;
}

.avatars {
  display: flex;
  justify-content: center;
  gap: 10px;
}

.avatars img {
  width: 50px;
  height: 50px;
  cursor: pointer;
  border: 2px solid transparent;
  border-radius: 50%;
}

.avatars img.selected {
  border-color: #007bff;
}
</style>