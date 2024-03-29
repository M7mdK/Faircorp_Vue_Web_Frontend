<template>
  <div class="window border border-secondary rounded shadow-sm p-2 mb-2 bg-white" :class="{expanded: isExpanded}">
    <div class="top-row d-flex" v-on:click="toggleExpand">
      <div class="window-name fw-bold pe-3">{{window.name}}</div>
      <div class="room-name text-muted">{{window.roomName}}</div>

      <div class="open-status ms-4" :class="{open: isWindowOpen, closed: !isWindowOpen}">
        <template v-if="isWindowOpen">
          <span class="icon">&#x2B24;</span> Open
        </template>
        <template v-else>
          <span class="icon">&#x2716;</span> Closed
        </template>
      </div>
      <div class="expand-button ms-auto">
        {{ isExpanded ? '&#9660;' : '&#9658;' }}
      </div>
    </div>
    <template v-if="isExpanded">
      <hr/>
      <div class="details d-flex">
        <button type="button" class="btn p-2 btn-secondary me-2" v-on:click="switchWindow">{{ isWindowOpen ? 'Close' : 'Open' }} This Window</button>
        <button type="button" class="btn p-2 btn-danger me-2" v-on:click="deleteWindow">Delete This Window</button>
      </div>
    </template>
  </div>
</template>

<script>
import axios from 'axios';
import {API_HOST} from '../config';

export default {
  name: 'WindowsListItem',
  props: ['window'],
  data: function() {
    return {
      isExpanded: false
    }
  }, 
  computed: {
    isWindowOpen: function() {
      return this.window.windowStatus === 'OPEN'; 
    }
  },
  methods: {
    toggleExpand() {
      this.isExpanded = !this.isExpanded;
    },
    async switchWindow() {
      let response = await axios.put(`${API_HOST}/api/windows/${this.window.id}/switch`);
      let updatedWindow = response.data;
      this.$emit('window-updated', updatedWindow);
    },
    async deleteWindow(){
      await axios.delete(`${API_HOST}/api/windows/${this.window.id}`);
      location.reload()
    },
  }
}
</script>



<style lang="scss" scoped>
.open-status {
  .icon {
    position: relative;
  }
  &.open {
    color: #00ff00;
    .icon {
      font-size: 12px;
      top: -3px;
    }
  }
  &.closed {
    color: #ff0000;
  }
}
.window {
  .top-row {
    cursor: pointer;
  }
}
</style>
