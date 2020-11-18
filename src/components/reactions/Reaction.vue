<template>
  <div class="dropdown-outer" @click="sendMessage(emoji)" @keyup.enter="sendMessage(emoji)" tabindex="0" :aria-label="label" role="button">
    <div class="dropdown-item" tabindex="-1" :class="{ faded: !canPost }">
      <img class="emoji" :src="getEncoded" :alt="getLabel"/>
      <span class="tooltiptext">{{ getLabel }}</span>
    </div>
  </div>
</template>

<script>
import { generateUUID } from "../../utils/index";

let ORDER;

let order;

export default {
  props: {
    encoded: String,
    text: String,
    label: String
  },

  computed: {
    canPost() {
      return this.$store.state.messages.filter((h) => h.owner === true).length < 1;
    },
    getEncoded() {
          return `${this.encoded}`;
    },
    getLabel() {
          return `${this.label}`;
    },
    getUsername() {
      if (this.$store.state.isFullName) {
        return this.$store.getters.getUser("fullName");
      } else {
        return this.$store.getters.getUser("name");
      }
    },
  },
  methods: {
    sendMessage(emoji) {
      this.$store.dispatch("closeDropdown", "reactions");
      if (this.canPost) {
        this.$store.dispatch("addMessage", {
          messageId: generateUUID(),
          encoded: this.getEncoded,
          username: this.getUsername,
          img: this.$store.getters.getUser("avatar"),
          owner: true
        });

        this.$socket.sendObj({
          action: "MESSAGE",
          message: {
            id: this.$store.getters.getUser("meetingID"),
            encoded: this.getEncoded,
            username: this.getUsername,
            img: this.$store.getters.getUser("avatar")
          },
        });
      }
    },
  },


Order: {
  const: ORDER = "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEsAAABLCAYAAAA4TnrqAAAACXBIWXMAAC4jAAAuIwF4pT92AAAJv2lUWHRYTUw6Y29tLmFkb2JlLnhtcAAAAAAAPD94cGFja2V0IGJlZ2luPSLvu78iIGlkPSJXNU0wTXBDZWhpSHpyZVN6TlRjemtjOWQiPz4gPHg6eG1wbWV0YSB4bWxuczp4PSJhZG9iZTpuczptZXRhLyIgeDp4bXB0az0iQWRvYmUgWE1QIENvcmUgNi4wLWMwMDIgNzkuMTY0MzUyLCAyMDIwLzAxLzMwLTE1OjUwOjM4ICAgICAgICAiPiA8cmRmOlJERiB4bWxuczpyZGY9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkvMDIvMjItcmRmLXN5bnRheC1ucyMiPiA8cmRmOkRlc2NyaXB0aW9uIHJkZjphYm91dD0iIiB4bWxuczp4bXA9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC8iIHhtbG5zOmRjPSJodHRwOi8vcHVybC5vcmcvZGMvZWxlbWVudHMvMS4xLyIgeG1sbnM6eG1wTU09Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9tbS8iIHhtbG5zOnN0RXZ0PSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VFdmVudCMiIHhtbG5zOnN0UmVmPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VSZWYjIiB4bWxuczpwaG90b3Nob3A9Imh0dHA6Ly9ucy5hZG9iZS5jb20vcGhvdG9zaG9wLzEuMC8iIHhtbG5zOnRpZmY9Imh0dHA6Ly9ucy5hZG9iZS5jb20vdGlmZi8xLjAvIiB4bWxuczpleGlmPSJodHRwOi8vbnMuYWRvYmUuY29tL2V4aWYvMS4wLyIgeG1wOkNyZWF0b3JUb29sPSJBZG9iZSBQaG90b3Nob3AgMjEuMSAoV2luZG93cykiIHhtcDpDcmVhdGVEYXRlPSIyMDIwLTA0LTI4VDE5OjUxOjM4KzAxOjAwIiB4bXA6TWV0YWRhdGFEYXRlPSIyMDIwLTA0LTI4VDIwOjAwOjQ3KzAxOjAwIiB4bXA6TW9kaWZ5RGF0ZT0iMjAyMC0wNC0yOFQyMDowMDo0NyswMTowMCIgZGM6Zm9ybWF0PSJpbWFnZS9wbmciIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6ZjE5OTUzZmYtOGViZC1jNTRjLWI2MTQtYWNjM2ZhMjI2OWZjIiB4bXBNTTpEb2N1bWVudElEPSJhZG9iZTpkb2NpZDpwaG90b3Nob3A6NTQyYTE1MzgtMWUwYy0xYTRmLTg4MzktNWRjNTc1ZWVmZjVjIiB4bXBNTTpPcmlnaW5hbERvY3VtZW50SUQ9InhtcC5kaWQ6ZGJlNTdlNDctZDU4ZC03YTQxLTkyY2EtZGExMGY3ZWJkMWMyIiBwaG90b3Nob3A6Q29sb3JNb2RlPSIzIiBwaG90b3Nob3A6SUNDUHJvZmlsZT0ic1JHQiBJRUM2MTk2Ni0yLjEiIHRpZmY6T3JpZW50YXRpb249IjEiIHRpZmY6WFJlc29sdXRpb249IjMwMDAwMDAvMTAwMDAiIHRpZmY6WVJlc29sdXRpb249IjMwMDAwMDAvMTAwMDAiIHRpZmY6UmVzb2x1dGlvblVuaXQ9IjIiIGV4aWY6Q29sb3JTcGFjZT0iMSIgZXhpZjpQaXhlbFhEaW1lbnNpb249IjUyNiIgZXhpZjpQaXhlbFlEaW1lbnNpb249IjUyNiI+IDx4bXBNTTpIaXN0b3J5PiA8cmRmOlNlcT4gPHJkZjpsaSBzdEV2dDphY3Rpb249ImNyZWF0ZWQiIHN0RXZ0Omluc3RhbmNlSUQ9InhtcC5paWQ6ZGJlNTdlNDctZDU4ZC03YTQxLTkyY2EtZGExMGY3ZWJkMWMyIiBzdEV2dDp3aGVuPSIyMDIwLTA0LTI4VDE5OjUxOjM4KzAxOjAwIiBzdEV2dDpzb2Z0d2FyZUFnZW50PSJBZG9iZSBQaG90b3Nob3AgMjEuMSAoV2luZG93cykiLz4gPHJkZjpsaSBzdEV2dDphY3Rpb249InNhdmVkIiBzdEV2dDppbnN0YW5jZUlEPSJ4bXAuaWlkOjJhNzJlYWExLWQzZWQtM2E0Zi04YjljLTY5YTc4Y2MyMGI0MyIgc3RFdnQ6d2hlbj0iMjAyMC0wNC0yOFQyMDowMDo0NyswMTowMCIgc3RFdnQ6c29mdHdhcmVBZ2VudD0iQWRvYmUgUGhvdG9zaG9wIDIxLjEgKFdpbmRvd3MpIiBzdEV2dDpjaGFuZ2VkPSIvIi8+IDxyZGY6bGkgc3RFdnQ6YWN0aW9uPSJjb252ZXJ0ZWQiIHN0RXZ0OnBhcmFtZXRlcnM9ImZyb20gYXBwbGljYXRpb24vdm5kLmFkb2JlLnBob3Rvc2hvcCB0byBpbWFnZS9wbmciLz4gPHJkZjpsaSBzdEV2dDphY3Rpb249ImRlcml2ZWQiIHN0RXZ0OnBhcmFtZXRlcnM9ImNvbnZlcnRlZCBmcm9tIGFwcGxpY2F0aW9uL3ZuZC5hZG9iZS5waG90b3Nob3AgdG8gaW1hZ2UvcG5nIi8+IDxyZGY6bGkgc3RFdnQ6YWN0aW9uPSJzYXZlZCIgc3RFdnQ6aW5zdGFuY2VJRD0ieG1wLmlpZDpmMTk5NTNmZi04ZWJkLWM1NGMtYjYxNC1hY2MzZmEyMjY5ZmMiIHN0RXZ0OndoZW49IjIwMjAtMDQtMjhUMjA6MDA6NDcrMDE6MDAiIHN0RXZ0OnNvZnR3YXJlQWdlbnQ9IkFkb2JlIFBob3Rvc2hvcCAyMS4xIChXaW5kb3dzKSIgc3RFdnQ6Y2hhbmdlZD0iLyIvPiA8L3JkZjpTZXE+IDwveG1wTU06SGlzdG9yeT4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6MmE3MmVhYTEtZDNlZC0zYTRmLThiOWMtNjlhNzhjYzIwYjQzIiBzdFJlZjpkb2N1bWVudElEPSJ4bXAuZGlkOmRiZTU3ZTQ3LWQ1OGQtN2E0MS05MmNhLWRhMTBmN2ViZDFjMiIgc3RSZWY6b3JpZ2luYWxEb2N1bWVudElEPSJ4bXAuZGlkOmRiZTU3ZTQ3LWQ1OGQtN2E0MS05MmNhLWRhMTBmN2ViZDFjMiIvPiA8L3JkZjpEZXNjcmlwdGlvbj4gPC9yZGY6UkRGPiA8L3g6eG1wbWV0YT4gPD94cGFja2V0IGVuZD0iciI/PrZEwTwAABNMSURBVHic3Zx5dFXVvcc/+5xz5yE38wgJYR5EZhVQnBGwDi1aau3rqpWq1efrqvhWW1fb5yvP0haftrUOYGtXW9tatMUWXxWVUaAqIASRKWJCICOZ7jydc94fJ8O9yc2cIPhd666Vu4ff/uV79/7t3/7tQfgWr+VTQCYwoe1TAhQAWYAHcLd9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",
  computed: {
    getOrder() {
      return ORDER;
    },
    getUsername() {
      if (this.$store.state.isFullName) {
        return this.$store.getters.getUser("fullName");
      } else {
        return this.$store.getters.getUser("name");
      }
    }
  },
  methods: {
    sendOrder() {
      // Close dropdown
      this.$store.dispatch("closeDropdown", "reactions");
      //Generate id
      const id = generateUUID();
      // Send local version to store
      if (this.$store.state.response.filter(h => h.owner === true).length < 1) {
        this.$store.dispatch("addOrder", {
          emoji: "response",
          encoded: ORDER,
          username: `${this.getUsername} raises a Point of Order`,
          img: this.$store.getters.getUser("avatar"),
          messageId: id,
          owner: true
        });
        // Send one over the websocket to other users
        this.$socket.sendObj({
          action: "ORDERQUEUE",
          message: {
            id: this.$store.getters.getUser("meetingID"),
            emoji: "response",
            encoded: ORDER,
            username: `${this.getUsername} raises a Point of Order`,
            img: this.$store.getters.getUser("avatar"),
            messageId: id
          }
        });
      }
    },
    closeDropdown() {
      this.$store.dispatch("closeDropdown", "reactions");
    }
  }
},
}

</script>

<style lang="scss" scoped>
.dropdown-outer {
  flex-basis: 25%;
  flex-shrink: 0;
  &:focus > .dropdown-item {
    background-color: rgba(2, 191, 165, 0.15);
    outline: 4px solid rgb(2, 191, 165);
  }
}

.dropdown-item {
  padding: 10px 18px;
  font-size: 15px;
  display: flex;
  align-items: center;
  position: relative;
  justify-content: center;
  border-radius: 8px;
  margin: 3px;
  &:hover {
    background-color: #00796b0d;
  }
}

.faded {
  opacity: 0.3;
  cursor: default;
  &:hover {
    background-color: white;
  }
}

.emoji {
  width: 100%;
}

.dropdown-item .tooltiptext {
  visibility: hidden;
  width: 200px;
  background-color: #cdcdcd;
  color: #000000;
  text-align: center;
  border-radius: 6px;
  padding: 5px 0;

  /* Position the tooltip */
  position: absolute;
  z-index: 1;
  bottom: -70px;
  left: 0%;
}

.dropdown-item:hover .tooltiptext {
  visibility: visible;
}
</style>
