<template>
  <div>
    <div class="Subhead">
      <h2 class="Subhead-heading">{{event.title}}</h2>
      <!-- /* TODO dedupe event-tags */ -->
      <div class="event-tags f6">
        <span href="#" class="event-tag" v-for="category in event.categories">{{category}}</span>
        <span href="#" class="event-tag" v-for="topic in event.topics">{{topic}}</span>
      </div>
    </div>
    <div class="clearfix mt-4">
      <div class="col-lg-3 col-md-4 pr-4 float-left">
        <div class="Box Box--condensed info-box">
          <ul>
            <li class="Box-row info-row">
              <span class="info-icon">
                <octicon name="calendar"></octicon>
              </span>
              <span class="info-text">
                <strong>{{startDate}}</strong> ({{day(event.start)}})
                <span v-if="startDate !== endDate"> ~ <strong>{{endDate}}</strong> ( ({{day(event.end)}}))</span>
              </span>
            </li>
            <li class="Box-row info-row" v-if="event.time && event.time.length > 0">
              <span class="info-icon">
                <octicon name="clock"></octicon>
              </span>
              <div class="info-text">
                <div v-for="t of event.time" class="info-time">
                  <strong>{{formatTime(t.from)}} ~ {{formatTime(t.to)}}{{t.after ? '++' : ''}}</strong>
                  <span class="info-extended text-gray f6" v-if="t.agenda">{{t.agenda}}</span>
                </div>
              </div>
            </li>
            <li class="Box-row info-row">
              <span class="info-icon">
                <octicon name="location"></octicon>
              </span>
              <span class="info-text">
                <a target="_blank" :href="event.location.url">{{event.location.title}}</a>
                <span class="info-extended text-gray f6" v-if="event.location.detail">{{event.location.detail}}</span>
              </span>
            </li>
          </ul>
        </div>

        <nav class="menu mt-4">
          <span class="menu-heading">Links</span>
          <a v-for="link in event.links" :href="link.url" class="menu-item">
            {{link.title}} <span class="text-gray f6">({{link.type}})</span>
            <span class="info-extended text-gray f6" v-if="link.detail">{{link.detail}}</span>
            <span class="info-extended text-gray f6" v-if="link.price">{{link.price}}</span>
          </a>
        </nav>
      </div>
      <div class="col-lg-9 col-md-8 float-left">
        <div class="markdown-body" v-html="descriptionHtml"></div>
        <p class="text-right mt-4">
          <a :href="editLink" class="btn btn-secondary">Edit on GitHub</a>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import MarkdownIt from 'markdown-it'
import Octicon from 'vue-octicon/components/Octicon.vue'

const md = MarkdownIt()

export default {
  props: [ 'event' ],
  methods: {
    formatDate (d) {
      const MONTHS = [
        'January',
        'February',
        'March',
        'April',
        'May',
        'June',
        'July',
        'August',
        'September',
        'October',
        'November',
        'December'
      ]
      return `${MONTHS[d.month - 1]} ${d.date}`
    },
    day (d) {
      const date = new Date(d.year, d.month - 1, d.date)
      return ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'][date.getDay()]
    },
    formatTime (t) {
      return t.hour + ':' + (t.minute < 10 ? '0' : '') + t.minute
    }
  },
  computed: {
    startDate () {
      return this.formatDate(this.event.start)
    },
    endDate () {
      return this.formatDate(this.event.end)
    },
    editLink () {
      return `https://thaiprogrammer-tech-events-calendar.spacet.me/go/?edit=${this.event.id}`
    },
    href () {
      return `/event/${this.event.id}`
    },
    descriptionHtml () {
      return md.render('> ' + this.event.summary + '\n\n' + this.event.description)
    }
  },
  components: {
    Octicon
  }
}
</script>

<style scoped>
.info-box .info-row {
  padding: 8px 10px;
  display: flex;
}
.info-icon {
  margin-right: 5px;
}
.info-extended {
  display: block;
  padding-left: 12px;
}

/* TODO dedupe event-tags */
.event-tags {
  display: flex;
  flex-wrap: wrap;
  align-items: baseline;
}
.event-tag {
  border-radius: 3px;
  background: #FAF8D1;
  color: #f49200;
  padding: 0.3em 0.9em;
  margin-right: 0.5em;
  margin-top: 0.5em;
  white-space: nowrap;
  text-decoration: none;
}
</style>