<template>
  <v-card
    class="elevation-4"
    style="top:20px"
  >

    <v-layout
      justify-space-between
      pa-3
    >
      <v-flex
        xs12
        sm12
        md5
      >
        <v-treeview
          :items="items"
          :active.sync="active"
          activatable
          class="grey--text text--darken-4 caption"
          active-class="white"
          hoverable
          open-all
          transition
        >
          <template
            slot="prepend"
            slot-scope="{ item }"
          >
            <span
              :class="item.selected? 'black--text text--darken-2' : 'grey--text text--darken-1 font-weight-thin'"
              style="font-size:14px"
            >
              {{ item.tmpname }}
            </span>
          </template>
        </v-treeview>
      </v-flex>

      <v-flex
        d-flex
        text-xs-center
      >
        <v-scroll-y-transition mode="out-in">
          <div
            v-if="!selected"
            class="title grey--text text--lighten-1 font-weight-light"
            style="align-self: center;"
          >
            Select a Cell Line
          </div>
          <v-card
            v-else
            :key="selected.id"
            class="pt-4 mx-auto"
            flat
            max-width="600"
          >
            <v-card-text>
              <!-- <v-avatar
                v-if="avatar"
                size="88"
              >
                <v-img
                  :src="`https://avataaars.io/${avatar}`"
                  class="mb-4"
                />
              </v-avatar> -->

              <CellLogo :name="selected.categary" />

              <h2
                class="mb-2"
                style="font-size:20px:font-weight:bold"
              >
                Cell Line Index: {{ selected.id }}
              </h2>
              <div class="blue--text mb-2">Trace ID: {{ selected.code_id }}</div>
              <div class="blue--text mb-2">Lab Source: {{ selected.labs }}</div>
            </v-card-text>
            <v-divider />
            <v-layout
              tag="v-card-text"
              wrap
            >
              <v-flex
                v-for="(item,index) in cardsname"
                v-if="selected[item.name] === 'TRUE'"
                :key="index"
                xs12
                sm6
                md4
              >
                <v-card
                  height="70"
                  class="elevation-3"
                >
                  <v-container
                    justify-center
                    fill-height
                  >
                    <span style="font-size:15px"> {{ item.name.toUpperCase() }}</span>
                  </v-container>
                </v-card>
              </v-flex>
            </v-layout>
            <v-divider />
            <v-layout
              tag="v-card-text"
              wrap
            >
              More information could be presented here...
            </v-layout>
          </v-card>
        </v-scroll-y-transition>
      </v-flex>
    </v-layout>
  </v-card>
</template>

<script>
import CellLogo from '~/components/CellLogo'

export default {
  components: {
    CellLogo: CellLogo
  },
  props: {
    data: {
      default: () => [],
      type: Array
    },
    filterdata: {
      default: () => [],
      type: Array
    }
  },
  data: () => ({
    active: [],
    avatar: null,
    open: [],
    users: [],
    cardsname: [
      { name: 'epic' },
      { name: 'rna_seq' },
      { name: 'wgs' },
      { name: 'atac_seq' }
    ]
  }),
  computed: {
    items: function() {
      // window.alert('test')
      // this.data.sort(function(a, b) {
      //   return a.code_index > b.code_index
      // })
      const tree = []
      // tree['Patients'] = { name: 'Patients', children: [] }

      this.filterdata.forEach(x => {
        var split_id = x['code_id'].split('-')
        var split_index = x['code_index'].split('-')
        var split_ca = x['code_ca'].split('-')

        var p = tree
        for (var i = 0; i < split_id.length; i++) {
          if (i === 0) {
            var tmpname = 'Patient-' + split_id[i]
          } else {
            var tmpname = split_ca[i] + '-' + split_id[i]
          }

          // console.log(p)
          if (!p.find(x => x.tmpname === tmpname)) {
            p.push({
              name: '',
              tmpname: tmpname,
              id: split_id[i],
              category: split_ca[i],
              selected: this.filterdata.find(x => x.id === split_id[i])
                ? true
                : false,
              color: 'blue',
              children: []
            })
          }
          p = p[p.findIndex(x => x.tmpname == tmpname)].children
        }
      })
      // console.log('------------------')
      // console.log(tree)
      return tree
    },
    selected() {
      if (!this.active.length) return undefined
      const id = this.active[0]
      return this.data.find(x => x.id === id)
    }
  }
}
</script>
