<template>
  <table class="mail-table">
    <tbody>
      <tr v-for="email in unarchivedEmails"
          :key="email.id"
          :class="[clickable, email.read ? 'read' : '']"
          @click="readEmail(email)">
        <td>
          <input type="checkbox" />
        </td>
        <td>{{ email.from }}</td>
        <td>
          <p><strong>{{ email.subject }}</strong></p>
        </td>
        <td class="date">{{ format(new Date(email.sentAt), 'MMM do yyyy') }}</td>
        <td><button @click="email.archived = true">Archive</button></td>
      </tr>
    </tbody>
  </table>
</template>

<script>
import { format } from 'date-fns'
import axios from 'axios'

export default {
  name: 'MailTable',
  props: {
    msg: String
  },
  async setup(){
    let { data:emails } = await axios.get('http://localhost:3000/emails')
    return {
      format,
      emails
    }
  },
  computed: {
    sortedEmails() {
      return this.emails.sort((e1, e2) => {
        return e1.sentAt < e2.sentAt ? 1: -1
      })
    },
    unarchivedEmails() {
      return this.sortedEmails.filter(e => !e.archived)
    }
  },
  methods: {
    readEmail(email) {
      email.read = true
      this.updateEmail(email)
    },
    archivedEmail(email) {
      email.archived = true
      this.updateEmail(email)
    },
    updateEmail(email) {
      axios.put(`http://localhost:3000/emails/${email.id}`, email)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
