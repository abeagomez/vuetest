<template>
  <v-card
    v-if="!($store.state.activeListCreateCard === this.list._id)"
    flat
    ripple
    @click.stop="activateCardMode"
    :color="cardHover ? 'grey lighten-1' : 'grey lighten-2'"
    :style="'text-decoration: ' +
            (cardHover ? 'underline' : 'none') +
            '; cursor: pointer; color: ' +
            (cardHover ? 'black' : 'grey') +
            ';'"
    @mouseenter="cardHover = true"
    @mouseleave="cardHover = false"
  >
    <v-container pa-2>
      <v-layout>
        <v-flex v-if="$vuetify.breakpoint.xsOnly" xs12 pl-3>
          <span class="headline">Add a card...</span>
        </v-flex>
        <v-flex v-else pt-0 pb-0>
          <span class="pa-0 caption">Add a card...</span>
        </v-flex>
      </v-layout>
    </v-container>
  </v-card>
  <v-card v-else flat color="grey lighten-2" @click.stop>
    <v-container pt-1 pl-0 pr-0 pb-1>
      <v-layout row>
        <v-flex xs12 pt-1>
          <v-form ref="form" v-model="valid" @submit.prevent="createCard()">
            <v-text-field
              class="caption"
              ref="newCardContent"
              v-model="card.content"
              :rules="[notEmptyRules]"
              required
              autofocus
              solo
              color="secondary"
              append-icon="save"
              @click:append="createCard()"
            />
          </v-form>
        </v-flex>
      </v-layout>
    </v-container>
  </v-card>
</template>

<script>
import { mapState, mapActions } from 'vuex';
import { notEmptyRules } from '../rules';

export default {
  name: 'card-create',
  props: ['list'],
  data: () => ({
    valid: false,
    cardHover: false,
    card: {
      content: '',
    },
    notEmptyRules,
  }),
  computed: {
    ...mapState('cards', { creatingCard: 'isCreatePending' }),
  },
  methods: {
    ...mapActions(['setActiveListCreateCard']),
    activateCardMode() {
      // eslint-disable-next-line
      this.setActiveListCreateCard(this.list._id);
      this.$emit('deactivateCreateMode');
      this.cardHover = false;
    },
    createCard() {
      if (this.valid) {
        const { Card } = this.$FeathersVuex;
        const card = new Card(this.card);
        // eslint-disable-next-line
        card.listId = this.list._id;
        card.boardId = this.list.boardId; // for server hooks
        card.save().then(() => {
          this.$refs.form.reset();
          this.$refs.newCardContent.focus();
        });
      }
    },
  },
};
</script>
