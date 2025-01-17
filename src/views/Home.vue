<template>
  <v-container class="pa-md-12">
    <v-row wrap align-content-space-around>
      <v-col cols="12">
        <h1
          :class="{
            'headline font-weight-black primary--text mb-3':
              $vuetify.breakpoint.smAndDown,
            'display-2 font-weight-black primary--text mb-3':
              $vuetify.breakpoint.mdAndUp
          }"
          tabindex="0"
        >
          ברוכים הבאים לוועדה פתוחה
        </h1>
      </v-col>
    </v-row>
    <v-row>
      <v-col>
        <p tabindex="0">
          ועדה פתוחה היא פלטפורמה לשיתוף ציבור וכלי להשפעה על תהליכי התכנון
          והבנייה. המערכת מנגישה את סדרי היום של ועדות התכנון ומאפשרת לכם -
          תושבות ותושבים, פעילי ארגונים סביבתיים, מתכננות, מתכננים ואנשי מקצוע
          אחרים - להשתתף באופן פעיל בתהליכי קבלת ההחלטות בתחום התכנון.
        </p>
        <p tabindex="0">
          כאן תוכלו להירשם להתראות מדיוני הוועדות המחוזיות והארציות, ולקבל
          באימייל את רשימת התכניות שיועלו לדיוני הוועדות בזמן אמת. בעמודי
          התכניות תוכלו להביע את התייחסויותיכם לתכנית עוד לפני שלבי ההתנגדויות,
          וכך להביע את עמדתכם באופן אפקטיבי ובזמן רלוונטי.
          <br />
          <router-link to="/about">למידע נוסף</router-link>
        </p>
      </v-col>
    </v-row>
    <v-row>
      <v-col>
        <v-btn color="secondary" x-large :to="subscriptionDestination">
          <v-icon left>mdi-bell-plus</v-icon>
          צרו התראות חדשות
        </v-btn>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12">
        <h2 class="headline primary--text font-weight-bold my-3" tabindex="0">
          הישיבות הקרובות במערכת
        </h2>
        <MeetingCards :meetings="upcomingMeetingCards"></MeetingCards>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import MeetingCards from "../components/MeetingCards";
import Component from "vue-class-component";
import Vue from "vue";
import { Getter, Action } from "vuex-class";
import { Getters, ActionTypes } from "../helpers/constants";
@Component({
  components: { MeetingCards }
})
export default class Home extends Vue {
  @Action(ActionTypes.FETCH_MANAGABLE_MEETINGS) fetchManagableMeetings;
  /** @type {import("../../graphql/types").Meeting[]} */
  @Getter(Getters.UPCOMING_MEETINGS) upcomingMeetings;
  /** @type {import("../../graphql/types").Meeting[]} */
  @Getter(Getters.MANAGABLE_MEETINGS) managableMeetings;
  @Getter(Getters.USER) user;
  async mounted() {
    await this.fetchManagableMeetings();
  }
  /** @type {import("../helpers/typings").MeetingCard[]} */
  get upcomingMeetingCards() {
    return this.upcomingMeetings.map(meeting => ({
      id: meeting.id,
      headline: meeting.committee.sid,
      date: meeting.date,
      isEditable:
        this.$vuetify.breakpoint.mdAndUp &&
        this.managableMeetings.some(
          managableMeeting => managableMeeting.id == meeting.id
        )
    }));
  }
  get subscriptionDestination() {
    return this.user ? "/subscriptions" : "/login";
  }
}
</script>
