<template>
  <v-app id="keep">
    <v-navigation-drawer v-model="drawer" fixed clipped class="grey lighten-4" app>
      <v-list dense class="grey lighten-4">
        <template v-for="(item, i) in items">
          <v-layout v-if="item.heading" :key="i" row align-center>
            <v-flex xs6>
              <v-subheader v-if="item.heading">{{ item.heading }}</v-subheader>
            </v-flex>
            <v-flex xs6 class="text-xs-right">
              <v-btn small flat>edit</v-btn>
            </v-flex>
          </v-layout>
          <v-divider v-else-if="item.divider" :key="i" dark class="my-3"></v-divider>
          <v-list-tile v-else :key="i" @click="drawer = !drawer">
            <v-list-tile-action>
              <v-icon>{{ item.icon }}</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title class="grey--text">{{ item.text }}</v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
        </template>
      </v-list>
    </v-navigation-drawer>
    <v-toolbar color="amber" app absolute clipped-left>
      <v-toolbar-side-icon @click="drawer = !drawer"></v-toolbar-side-icon>
      <span class="title ml-3 mr-5">
        お部屋在庫&nbsp;
        <span class="font-weight-light">管理</span>
      </span>
      <v-text-field
        solo-inverted
        flat
        hide-details
        label="Search"
        prepend-inner-icon="search"
        v-model="search"
      ></v-text-field>
      <v-spacer></v-spacer>
    </v-toolbar>

    <v-content>
      <v-container fluid fill-height class="grey lighten-4">
        <v-layout justify-center align-center>
          <v-flex shrink>
            <v-card>
              <v-card-title>
                <span class="headline">何買う？</span>
              </v-card-title>

              <v-card-text>
                <v-container grid-list-md>
                  <v-layout wrap>
                    <v-flex shrink>
                      <v-text-field v-model="addingItem" label="買うもの"></v-text-field>
                    </v-flex>
                    <!-- <v-flex shrink>
                      <v-text-field v-model="addingCategory" label="カテゴリ"></v-text-field>
                    </v-flex>-->

                    <v-flex shrink>
                    <v-menu offset-y>
                      <template v-slot:activator="{ on }">
                        <v-btn light v-on="on">Dropdown</v-btn>
                      </template>
                      <v-list>
                        <v-list-tile v-for="(category, index) in categories" :key="index" @click="">
                          <v-list-tile-title>{{ category.text }}</v-list-tile-title>
                        </v-list-tile>
                      </v-list>
                    </v-menu>
                     </v-flex>
                  </v-layout>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" flat @click="save2">Save</v-btn>
              </v-card-actions>
            </v-card>
            <v-data-table
              :items="item"
              :headers="header"
              class="elevation-10"
              :customFilter="categoryFilter"
              :search="search"
            >
              <template slot="items" slot-scope="row">
                <td>
                  <v-edit-dialog
                    :return-value.sync="row.item.name"
                    lazy
                    @save="save"
                    @cancel="cancel"
                    @open="open"
                    @close="close"
                  >
                    {{ row.item.name }}
                    <template v-slot:input>
                      <v-text-field
                        v-model="row.item.name"
                        :rules="[max25chars]"
                        label="Edit"
                        single-line
                        counter
                      ></v-text-field>
                    </template>
                  </v-edit-dialog>
                </td>
                <td>{{row.item.score}}</td>
                <td>{{categories[row.item.category]}}</td>
              </template>
            </v-data-table>
            <v-snackbar v-model="snack" :timeout="3000" :color="snackColor">
              {{ snackText }}
              <v-btn flat @click="snack = false">Close</v-btn>
            </v-snackbar>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
/* eslint-disable */
export default {
  methods: {
    categoryFilter(items, search) {
      if (search == "") {
        return items;
      } else {
        return items.filter(el => el.score == search);
      }
    },
    save2() {
      const value = this.item.findIndex(el => el.name === this.addingItem);
      if (value === -1) {
        const i = { name: this.addingItem, score: 1, category: 2 };
        this.item.push(i);
      } else {
        this.item[value].score += 1;
      }
    },
    save() {
      this.snack = true;
      this.snackColor = "success";
      this.snackText = "Data saved";
    },
    cancel() {
      this.snack = true;
      this.snackColor = "error";
      this.snackText = "Canceled";
    },
    open() {
      this.snack = true;
      this.snackColor = "info";
      this.snackText = "Dialog opened";
    },
    close() {}
  },
  data: () => ({
    drawer: null,
    search: "",
    addingItem: "",
    items: [
      { icon: "lightbulb_outline", text: "Notes" },
      { icon: "touch_app", text: "Reminders" },
      { divider: true },
      { heading: "Labels" },
      { icon: "add", text: "Create new label" },
      { divider: true },
      { icon: "archive", text: "Archive" },
      { icon: "delete", text: "Trash" },
      { divider: true },
      { icon: "settings", text: "Settings" },
      { icon: "chat_bubble", text: "Trash" },
      { icon: "help", text: "Help" },
      { icon: "phonelink", text: "App downloads" },
      { icon: "code", text: "code" },
      { icon: "keyboard", text: "Keyboard shortcuts" }
    ],
    // header: [{ text: "名前", value: "name" }, { text: "個数", value: "score" },{ text: "カテゴリ"}],
    header: [
      { text: "名前", value: "name" },
      { text: "個数", value: "score" },
      { text: "カテゴリ", value: "category" }
    ],
    item: [{ name: "洗剤", score: 1, category: 2 }],
    categories
    : [{ 1: "食品" },{ 2: "家庭用品" }],
    snack: false,
    snackColor: "",
    snackText: "",
    max25chars: v => v.length <= 25 || "Input too long!"
  }),
  props: {
    source: String
  }
};
</script>

<style lang="stylus">
#keep {
  .v-navigation-drawer__border {
    display: none;
  }
}
</style>