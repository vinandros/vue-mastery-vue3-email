<template>
  <table class="mail-table">
    <tbody>
      <tr v-for="email in unarchiveEmails" 
        :key="email.id"
        :class="['clickable', email.read ? 'read': '']"
        @click="openEmail(email)"
      >
        <td>
        <input type="checkbox" />
        </td>
        <td>{{email.from}}</td>
        <td>
        <p><strong>{{email.subject}}</strong> - {{email.body}}</p>
        </td>
        <td class="date">{{format(new Date(email.sentAt), 'MMM do yyyy')}}</td>
        <td><button @click="archivedEmail(email)">Archive</button></td>
      </tr>
    </tbody>
  </table>
  <MailView v-if="openedEmail" :email="openedEmail" />
</template>

<script>
import { format } from 'date-fns'
import MailView from './MailView'
import axios from 'axios'
export default {
  name:"MainTable",
  async setup(){
    let { data: emails} = await axios.get('http://localhost:3000/emails')
    return {
        format,
        emails,
        openedEmail:null
    }
  },
  components:{
    MailView
  },
  computed:{
    sortedEmails(){
      return this.emails.sort((el1, el2) => (el1.sentAt < el2.sentAt ? 1 : -1))
    },
    unarchiveEmails(){
      return this.emails.filter(e => !e.archived)
    }
  },
  methods:{
    openEmail(email){
      email.read = true;
      this.updateEmail(email);
      this.openedEmail = email;
    },
    archivedEmail(email){
      email.archived = true;
      this.updateEmail(email)
    },
    updateEmail(email){
      axios.put('http://localhost:3000/emails/'+ email.id, email)
    }
  }
}

</script>

<style scoped>

</style>
