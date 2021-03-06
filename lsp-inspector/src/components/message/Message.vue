<template>
  <span class="msg" :class="[{ current: isCurrent }, item.msgKind]" @mouseover="updateCurrent">
    <div @click="toggleArg" class="msg-clickable-area">
      <span>
        <font-awesome-icon class="fa-icon" icon="comment" v-if="item.msgKind === 'send-request'" />
        <font-awesome-icon class="fa-icon" icon="comment-alt" v-if="item.msgKind === 'send-notification'" />
        <font-awesome-icon class="fa-icon" icon="comment" v-if="item.msgKind === 'send-response'" />
      </span>

      <span class="msg-type" v-if="isLeft">
        {{ item.msgType }}
      </span>

      <span class="msg-timestamp">{{ timestampOrLatency }}</span>

      <span class="msg-type" v-if="!isLeft">
        {{ item.msgType }}
      </span>

      <span>
        <font-awesome-icon class="fa-icon" icon="comment" transform="flip-h" v-if="item.msgKind === 'recv-response'" />
        <font-awesome-icon class="fa-icon" icon="comment-alt" tranform="flip-h" v-if="item.msgKind === 'recv-notification'" />
        <font-awesome-icon class="fa-icon" icon="comment" transform="flip-h" v-if="item.msgKind === 'recv-request'" />
      </span>
    </div>
    <message-detail :item="item" v-if="this.expanded" @click="noop"></message-detail>
  </span>
</template>


<script lang="ts">
import Vue from 'vue'
import FontAwesomeIcon from '@fortawesome/vue-fontawesome'
import MessageDetail from '@/components/message/MessageDetail.vue'

export default Vue.extend({
  name: 'message',
  components: {
    FontAwesomeIcon,
    MessageDetail
  },
  props: ['item'],
  data() {
    return {
      expanded: false
    }
  },

  computed: {
    isLeft() {
      return (
        this.item.msgKind === 'send-request' ||
        this.item.msgKind === 'send-notification' ||
        this.item.msgKind === 'send-response'
      )
    },
    timestampOrLatency() {
      return this.item.msgKind === 'recv-response' || this.item.msgKind === 'send-response'
        ? this.item.msgLatency
        : this.item.time
    },
    isCurrent() {
      return this.item.msgId === this.$store.state.current
    }
  },
  methods: {
    toggleArg() {
      this.expanded = !this.expanded
    },
    updateCurrent() {
      if (this.item.msgId) {
        this.$store.commit('updateCurrent', this.item.msgId)
      } else {
        this.$store.commit('updateCurrent', -1)
      }
    },
    noop() {}
  }
})
</script>

<style lang="scss" scoped>
@import '@/scss/global.scss';

.msg {
  font-family: $monospace;
  font-weight: 400;
  font-size: 13px;
  border: 1px solid #ddd;
  border-radius: 4px;
  margin-bottom: 20px;
  padding: 10px 20px;
}
.msg-timestamp {
  color: #e8a1a1;
  font-size: 11px;
}

.send-request,
.send-notification,
.send-response {
  align-self: flex-start;
  text-align: left;
}
.recv-response,
.recv-notification,
.recv-request {
  align-self: flex-end;
  text-align: right;
}

.send-request,
.recv-response {
  color: #1c791cbd;
}
.send-notification,
.recv-notification {
  color: #bb9911de;
}
.recv-request,
.send-response {
  color: #356fa5f7;
}

/**
 * Hover / Expand
 */

.msg {
  @include transition(all);
}
.msg:hover {
  background-color: rgba(221, 221, 221, 0.5);
  border-color: $active-bg;
}
.msg.current {
  background-color: rgba(221, 221, 221, 0.5);
  border-color: $active-bg;
}
.msg-clickable-area {
  cursor: pointer;
}
</style>
