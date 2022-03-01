<template>
  <div>
    <p>Google Calendar API Quickstart</p>

    <p id="content">ここに予定をだす</p>
    <button @click="handleAuthClick()">ログインボタン</button>
    <button @click="handleSignoutClick()">ログアウトボタン</button>
    <button @click="test()">初期化ボタン。あとで自動化する</button>
  </div>
</template>
<script lang='ts'>
import { defineComponent, onMounted } from '@vue/composition-api'
/// <reference path="../../../node_modules/@types/gapi/index.d.ts" />

// eslint-disable-next-line @typescript-eslint/no-namespace
declare namespace gapi {}
export default defineComponent({
  setup () {
    const CLIENT_ID = '307966429693-ojse5mu8mmndt0nh36p7c9t5jrdgj5j9.apps.googleusercontent.com'
    const API_KEY = 'AIzaSyALCsR1R9piZ_8bHaCoeleM3VuW6mD4EB8'
    const DISCOVERY_DOCS = ['https://www.googleapis.com/discovery/v1/apis/calendar/v3/rest']
    const SCOPES = 'https://www.googleapis.com/auth/calendar.readonly'
    const calendarID = 'saruwatari.bloomark@gmail.com'

    function test () {
      console.log('すたーと')
      gapi.load('client:auth2', initClient)
    }
    function initClient () {
      console.log('わん')

      gapi.client.init({
        apiKey: API_KEY,
        clientId: CLIENT_ID,
        discoveryDocs: DISCOVERY_DOCS,
        scope: SCOPES
      }).then(function () {
        console.log('ひゃあああ')
        // Listen for sign-in state changes.
        gapi.auth2.getAuthInstance().isSignedIn.listen(updateSigninStatus)

        // Handle the initial sign-in state.
        updateSigninStatus(gapi.auth2.getAuthInstance().isSignedIn.get())
      }, function (error) {
        console.log('えらあああ', error)

        appendPre(JSON.stringify(error, null, 2))
      })
    }
    function updateSigninStatus (isSignedIn:boolean) {
      console.log('あぷらでーとしぐんすてーたす', isSignedIn)
      if (isSignedIn) {
        listUpcomingEvents()
      }
    }
    function handleAuthClick () {
      // const user = gapi.auth2.getAuthInstance()
      // console.log(user)
      gapi.auth2.getAuthInstance().signIn().catch(e => { console.error(e) })
    }
    function handleSignoutClick () {
      console.log('ろぐあーうと', gapi)
      gapi.auth2.getAuthInstance().signOut()
    }
    function appendPre (message:any) {
      const pre:HTMLElement|null = document.getElementById('content')
      const textContent = document.createTextNode(message + '\n')
      if (pre) {
        pre.appendChild(textContent)
      }
    }
    function listUpcomingEvents () {
      console.log('イベントとってくるでー')
      gapi.client.calendar.events.list({
        calendarId: calendarID,
        timeMin: (new Date()).toISOString(),
        showDeleted: false,
        singleEvents: true,
        maxResults: 10,
        orderBy: 'startTime'
      }).then(function (response) {
        console.log(response)
        const events = response.result.items
        appendPre('Upcoming events:')

        if (events.length > 0) {
          for (let i = 0; i < events.length; i++) {
            const event = events[i]
            let when = event.start.dateTime
            if (!when) {
              when = event.start.date
            }
            appendPre(event.summary + ' (' + when + ')')
          }
        } else {
          appendPre('No upcoming events found.')
        }
      })
    }
    onMounted(() => {
      const script:any = document.createElement('script')
      script.src = 'https://apis.google.com/js/api.js'
      // script.setAttribute('src', 'https://apis.google.com/js/api.js')
      script.onreadystatechange = script.onload = function () {
        console.log('ろーどずみ')
      }

      document.head.appendChild(script)
    })
    return {
      handleAuthClick,
      handleSignoutClick,
      test
    }
  }
})
</script>
