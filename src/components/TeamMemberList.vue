<template>
  <transition-group name="list" tag="div" class="team-members-list">
    <template v-for="item in items" :key="getMemberProperty(item, 'displayed')">
      <div>
        <span
          v-if="item === 'ONLINE'"
          class="separator text-green-400"
          :data-was-shown="last.showOnline"
          :data-show="showOnline"
        >
          Live
        </span>
        <span
          v-else-if="item === 'OFFLINE'"
          class="separator text-red-400"
          :data-was-shown="last.showOffline"
          :data-show="showOffline"
        >
          Offline
        </span>
        <a
          v-else
          :href="`https://twitch.tv/${getMemberProperty(item, 'twitch')}`"
          class="text-gray-200 flex space-x-1 group"
          target="_blank"
          rel="noopener noreferrer"
        >
          <span>{{ getMemberProperty(item, "displayed") }}</span>
          <ExternalLinkIcon
            class="opacity-0 transition-opacity duration-200 group-hover:opacity-100"
            size="15"
            stroke-width="4"
          />
        </a>
      </div>
    </template>
  </transition-group>
</template>

<style scoped>
  .list-move {
    transition: 1s ease transform;
  }

  .separator {
    @apply block text-lg mt-3 uppercase transition-opacity opacity-0 duration-500
  }

  .separator[data-show="true"][data-was-shown="false"] {
    animation: 500ms ease fade-in normal both;
  }

  .separator[data-show="false"][data-was-shown="true"] {
    animation: 500ms ease fade-in reverse both;
  }

  .separator[data-show="true"][data-was-shown="true"] {
    opacity: 1;
  }

  .separator[data-show="false"][data-was-shown="false"] {
    opacity: 0;
  }

  @keyframes fade-in {
    from {
      opacity: 0;
    }

    to {
      opacity: 1;
    }
  }
</style>

<script>
  import { ExternalLinkIcon } from "@zhuowenli/vue-feather-icons"
  import { getMemberProperty } from "../assets/get-member-property"

  export default {
    name: "TeamMemberList",
    components: { ExternalLinkIcon },
    props: {
      online: {
        type: Array,
        required: true
      },
      offline: {
        type: Array,
        required: true
      }
    },
    data: () => ({
      showOnline: true,
      showOffline: true,
      last: null
    }),
    computed: {
      items() {
        const start = []
        const end = []

        if (this.online.length === 0) end.push("ONLINE")
        else start.push("ONLINE", ...this.online)

        if (this.offline.length === 0) end.push("OFFLINE")
        else start.push("OFFLINE", ...this.offline)

        return [...start, ...end]
      }
    },
    watch: {
      items: {
        immediate: true,
        handler() {
          if (this.last !== null) this.last = {
            showOnline: this.showOnline,
            showOffline: this.showOffline
          }

          this.showOffline = this.offline.length !== 0
          this.showOnline = this.online.length !== 0

          if (this.last === null) this.last = {
            showOnline: this.showOnline,
            showOffline: this.showOffline
          }
        }
      }
    },
    methods: {
      getMemberProperty
    }
  }
</script>
