<template>
  <v-card
    v-if="!createMode"
    flat
    ripple
    @click.stop="$emit('activateCreateMode')"
    :style="'opacity: ' + (cardHover ? '1' : '0.7') +'; cursor: pointer; color: white;'"
    pa-0
    @mouseenter="cardHover = true"
    @mouseleave="cardHover = false"
    color="grey darken-2"
  >
    <v-container pa-1>
      <v-layout column>
        <v-flex v-if="$vuetify.breakpoint.xsOnly" xs12 pl-3 pb-3 pt-3>
          <span class="headline">Add a list...</span>
        </v-flex>
        <v-flex v-else>
          <span class="pa-0 caption">Add a list...</span>
        </v-flex>
      </v-layout>
    </v-container>
  </v-card>
  <v-card v-else @click.stop="$emit('activateCreateMode')">
    <v-form
      ref="form"
      v-model="valid"
      @submit.prevent="createList"
      @keydown.prevent.enter
    >
      <v-card-title>
        <v-container pa-0>
          <v-layout column>
            <v-flex>
              <v-text-field
                ref="listname"
                v-model="list.name"
                :rules="[notEmptyRules]"
                label="Name"
                required
                autofocus
              ></v-text-field>
            </v-flex>
          </v-layout>
        </v-container>
      </v-card-title>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn
          color="success"
          type="submit"
          :loading="creating"
          :disabled="!valid || creating"
        >Create</v-btn>
      </v-card-actions>
    </v-form>
  </v-card>
</template>

<script>
import { mapState } from 'vuex';
import { notEmptyRules } from '../rules';

export default {
  name: 'list-create',
  props: ['createMode', 'board'],
  data: () => ({
    cardHover: false,
    valid: false,
    list: {},
    notEmptyRules,
  }),
  computed: {
    ...mapState('lists', { creating: 'isCreatePending' }),
  },
  methods: {
    createList() {
      if (this.valid) {
        const { List } = this.$FeathersVuex;
        const list = new List(this.list);
        // eslint-disable-next-line
        list.boardId = this.board._id;
        list.save().then(() => {
          this.$refs.form.reset();
        });
      }
    },
  },
};
</script>
