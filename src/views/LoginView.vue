<template>
  <div style="height: 100%;display: flex;flex-direction: column;justify-content: center">
    <div v-if="!isIn" style="display: flex;flex-direction: column;align-items: center">
      <input v-model="id" />
      <button @click="connection">Connection</button>
    </div>
    <div v-else style="display: flex;flex-direction: column;align-items: center">
      <div style="padding: 10px;border-width: 1px;border-style: solid">
        ID : {{ id }}
      </div>
      <div style="margin: 20px" v-for="(item, index) in messages" :key="index">
        {{item}}
      </div>
    </div>
  </div>
</template>

<script>
import { Client } from '@stomp/stompjs'

export default {
  data() {
    return {
      id: '',
      client: null,
      isIn: false,
      messages: []
    }
  },
  methods: {
    connection() {
      if(this.id.trim().length > 0){
        this.isIn = true
        this.id = this.id.trim()
        this.startConnection()
      } else {
        alert('ID를 입력해 주세요.')
      }
    },
    startConnection() {
      this.client = new Client({
        brokerURL: 'ws://localhost:8080/socket',
        onConnect: () => {
          this.client.subscribe(`topic/${this.id}`, message => {
            const body = message.body
            this.messages = [...this.messages, body]
            if('main' === body){
              this.$router.push('/main')
            }
          })
        }
      })
      this.client.activate()
    }
  }
}
</script>
