<template>
  <v-app>
    <v-toolbar dense :color="color" dark>
      <v-row>
        <v-btn icon @click="drawer = !drawer" class="sm12">
          <v-icon>mdi-menu</v-icon>
        </v-btn>

        <v-tooltip bottom z-index="10">
          <template v-slot:activator="{ on, attrs }">
            <v-btn icon @click="search_btn = !search_btn" v-bind="attrs" v-on="on">
              <v-icon>mdi-magnify</v-icon>
            </v-btn>
          </template>
          <span>Поиск</span>
        </v-tooltip>

        <div class="input-div" v-if="search_btn && $vuetify.breakpoint.name !== 'xs'">
          <v-text-field :placeholder="$t('search')" class="search_input">
          </v-text-field>
        </div>

        <v-tooltip bottom z-index="10">
          <template v-slot:activator="{ on, attrs }">
            <v-btn icon @click="date_btn = !date_btn" v-bind="attrs" v-on="on">
              <v-icon>mdi-calendar</v-icon>
            </v-btn>
          </template>
          <span>Поиск по дате</span>
        </v-tooltip>

        <div class="input-div" style="max-width: 325px; width:auto" v-if="date_btn && $vuetify.breakpoint.name !== 'xs'">
          <v-menu
            ref="menu"
            v-model="menu"
            :close-on-content-click="false"
            transition="scale-transition"
            offset-y
          >
            <template v-slot:activator="{ on, attrs }">
              <div>
                <v-text-field
                  class="search_input"
                  style="width: 180px!important;display: inline-block;top: -3px!important;position: relative;margin-right: 10px;"
                  v-model="dateFormat"
                  :placeholder="$t('date')"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                ></v-text-field>
                <v-btn icon
                       @click="time_show = !time_show"
                style="top: 15px;">
                  <v-icon>mdi-av-timer</v-icon>
                </v-btn>
                <v-text-field
                  v-if="time_show"
                  class="search_input"
                  style="width: 40px!important;display: inline-block;top: -5px!important;position: relative;margin-right: 10px;text-align: center;"
                  type="time"
                  :clear-icon="false"
                ></v-text-field>
                <v-text-field
                  v-if="time_show"
                  class="search_input"
                  style="width: 40px!important;display: inline-block;top: -5px!important;position: relative;text-align: center;"
                  type="time"
                ></v-text-field>
              </div>
            </template>
            <v-date-picker
              ref="picker"
              v-model="date"
              range
              :locale="language === 'ru' ? language : 'en'"
            ></v-date-picker>
          </v-menu>
        </div>

        <v-tooltip bottom z-index="10">
          <template v-slot:activator="{ on, attrs }">
            <v-btn icon v-bind="attrs" v-on="on">
              <v-icon>mdi-reload</v-icon>
            </v-btn>
          </template>
          <span>Обновить</span>
        </v-tooltip>

        <v-tooltip bottom z-index="10">
          <template v-slot:activator="{ on, attrs }">
            <v-btn icon v-bind="attrs" v-on="on">
              <v-icon>mdi-cog</v-icon>
            </v-btn>
          </template>
          <span>Настройки</span>
        </v-tooltip>

        <v-tooltip bottom z-index="10">
          <template v-slot:activator="{ on, attrs }">
            <v-btn icon v-bind="attrs" v-on="on">
              <v-icon>mdi-account-multiple</v-icon>
            </v-btn>
          </template>
          <span>Профили</span>
        </v-tooltip>

        <v-tooltip bottom z-index="10">
          <template v-slot:activator="{ on, attrs }">
            <v-btn icon v-bind="attrs" v-on="on">
              <v-icon>mdi-file-excel</v-icon>
            </v-btn>
          </template>
          <span>Экспорт</span>
        </v-tooltip>

        <div class="input-div" v-if="$vuetify.breakpoint.name !== 'xs'">
          <v-combobox
            disable-lookup
            :items="['Базовый', 'Новый']"
            placeholder="Выберите профиль"
            style="top: 5px; width: 170px;"
          ></v-combobox>
        </div>


        <v-tooltip bottom z-index="10" v-if="$vuetify.breakpoint.name !== 'xs'">
          <template v-slot:activator="{ on, attrs }">
            <v-btn icon v-bind="attrs" v-on="on">
              <v-icon>mdi-clipboard-plus</v-icon>
            </v-btn>
          </template>
          <span>Добавить отчет</span>
        </v-tooltip>

        <div class="input-div" v-if="$vuetify.breakpoint.name !== 'xs'">
          <v-combobox
            disable-lookup
            :items="['Отчет 1', 'Отчет 2']"
            placeholder="Выберите отчет"
            style="top: 5px; width: 145px;"
          ></v-combobox>
        </div>
      </v-row>
      <v-spacer></v-spacer>
      <v-btn icon to="/">
        <v-icon>mdi-exit-run</v-icon>
      </v-btn>
      <div class="text-center">
        <v-menu open-on-hover top offset-y>
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              v-bind="attrs"
              v-on="on"
              color="transparent"
            >
              <flag :iso="language" v-bind:squared="false"/>
            </v-btn>
          </template>

          <v-list>
            <v-list-item
              v-for="(item, index) in languages"
              :key="index"
              @click="changeLocale(item.language, item.flag)"
            >
              <v-list-item-title>{{ item.title }}</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-menu>
      </div>
    </v-toolbar>
    <v-navigation-drawer :color="'grey'" v-model="drawer" hide-overlay app absolute>
      <v-treeview
        :items="items"
        class="left-tree"
      ></v-treeview>
      <v-treeview
        :items="items"
        class="left-tree left-tree-bottom"
      ></v-treeview>
    </v-navigation-drawer>

    <v-content>
      <div class="top" :style="hide_text ? '' : 'height: calc(100vh - 50px)'">
        <div v-if="$vuetify.breakpoint.name === 'xs'">
          <div class="input-mobile">
            <v-combobox
              disable-lookup
              :items="['Базовый', 'Новый']"
              placeholder="Выберите профиль"
              style="top: 5px"
            ></v-combobox>
          </div>
          <div v-if="search_btn" class="input-mobile">
            <v-text-field :placeholder="$t('search')" class="search_input">
            </v-text-field>
          </div>
          <div class="input-mobile" v-if="date_btn">
            <v-menu
              ref="menu"
              v-model="menu"
              :close-on-content-click="false"
              transition="scale-transition"
              offset-y
              min-width="290px"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-text-field
                  class="search_input"
                  v-model="dateFormat"
                  :placeholder="$t('date')"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                ></v-text-field>
              </template>
              <v-date-picker
                ref="picker"
                v-model="date"
                range
                :locale="language === 'ru' ? language : 'en'"
              ></v-date-picker>
            </v-menu>
          </div>
        </div>
        <v-data-table
          show-select
          :headers="headers"
          :items="desserts"
          :items-per-page="1000000"
          fixed-header
          :height="hide_text ? '300px' : 'calc(100vh - 120px)'"
          :mobile-breakpoint="NaN"
        ></v-data-table>
        <span
          class="hide-text"
          @click="hide_text = !hide_text"
          :style="hide_text ? '' : 'bottom: 0px;'"
          v-if="$vuetify.breakpoint.name !== 'xs'"
        >
          <span v-if="hide_text">Скрыть текст</span>
          <span v-else>Показать текст</span>
        </span>
      </div>
      <div class="bottom" v-if="hide_text">
        <div class="text" style="overflow-y: scroll!important;height: 100%;">
          Новый текст с безумным описанием ...Новый текст с безумным описанием ...Новый текст с безумным описанием
          ...Новый текст с безумным описанием ...Новый текст с безумным описанием ...Новый текст с безумным описанием
          ...Новый текст с безумным описанием ...Новый текст с безумным описанием ...Новый текст с безумным описанием
          ...Новый текст с безумным описанием ...Новый текст с безумным описанием ...Новый текст с безумным описанием
          ...Новый текст с безумным описанием ...Новый текст с безумным описанием ...Новый текст с безумным описанием
          ...Новый текст с безумным описанием ...Новый текст с безумным описанием ...Новый текст с безумным описанием
          ...Новый текст с безумным описанием ...Новый текст с безумным описанием ...Новый текст с безумным описанием
          ...Новый текст с безумным описанием ...Новый текст с безумным описанием ...Новый текст с безумным описанием
          ...Новый текст с безумным описанием ...Новый текст с безумным описанием ...Новый текст с безумным описанием
          ...Новый текст с безумным описанием ...Новый текст с безумным описанием ...Новый текст с безумным описанием
          ...Новый текст с безумным описанием ...Новый текст с безумным описанием ...Новый текст с безумным описанием
          ...Новый текст с безумным описанием ...Новый текст с безумным описанием ...Новый текст с безумным описанием
          ...Новый текст с безумным описанием ...Новый текст с безумным описанием ...Новый текст с безумным описанием
          ...Новый текст с безумным описанием ...Новый текст с безумным описанием ...Новый текст с безумным описанием
          ...Новый текст с безумным описанием ...Новый текст с безумным описанием ...
        </div>
      </div>
    </v-content>
  </v-app>
</template>

<script>
  import moment from 'moment'

  export default {
    name: 'Home',
    mounted() {
      this.language = this.$i18n.locale;
    },
    methods: {
      changeLocale(locale, flag) {
        this.$i18n.locale = locale;
        this.language = flag;
      }
    },
    data() {
      return {
        hide_text: false,
        languages: [
          {flag: 'us', language: 'en', title: 'English'},
          {flag: 'ru', language: 'ru', title: 'Русский'}
        ],
        language: 'ru',
        menu: false,
        date: new Date(),
        dateFormat: '',
        time_show: false,
        items: [
          {
            id: 1, name: 'Тестоовый список 1', children: [
              {
                id: 2, name: 'Тестовый второй уровень', children: [
                  {id: 3, name: 'тестовый третий уровень'}
                ]
              }
            ]
          },
          {id: 4, name: 'Тестоовый список 2'},
          {
            id: 5, name: 'Тестоовый список 1', children: [
              {
                id: 6, name: 'Тестовый второй уровень', children: [
                  {id: 7, name: 'тестовый третий уровень'}
                ]
              }
            ]
          },
        ],
        drawer: this.$vuetify.breakpoint.name === 'xs' ? false : true,
        color: 'primary',
        colors: [
          'primary',
          'blue',
          'success',
          'red',
          'teal',
        ],
        right: false,
        permanent: true,
        miniVariant: false,
        expandOnHover: false,
        background: false,
        search_btn: false,
        date_btn: false,
        headers: [
          {text: this.$t('table_title'), value: 'title', width: '30%'},
          {text: this.$t('table_subject'), value: 'subject', width: '10%'},
          {text: this.$t('table_date'), value: 'date', width: '15%'},
          {text: this.$t('table_from'), value: 'from', width: '20%'},
          {text: this.$t('table_bal'), value: 'bal'},
          {text: this.$t('table_rel'), value: 'rel'},
        ],
        desserts: [
          {
            subject: '',
            title: 'Безумные английские учены нашли динозавров в голове у буратино!',
            date: '28.06.2020',
            from: 'vk.com СВАО бутырский',
            bal: '10',
            rel: '8',
          },
          {
            subject: '',
            title: 'Безумные английские учены нашли динозавров в голове у буратино!',
            date: '28.06.2020',
            from: 'vk.com СВАО бутырский',
            bal: '10',
            rel: '8',
          },
          {
            subject: '',
            title: 'Безумные английские учены нашли динозавров в голове у буратино!',
            date: '28.06.2020',
            from: 'vk.com СВАО бутырский',
            bal: '10',
            rel: '8',
          },
          {
            subject: '',
            title: 'Безумные английские учены нашли динозавров в голове у буратино!',
            date: '28.06.2020',
            from: 'vk.com СВАО бутырский',
            bal: '10',
            rel: '8',
          },
          {
            subject: '',
            title: 'Безумные английские учены нашли динозавров в голове у буратино!',
            date: '28.06.2020',
            from: 'vk.com СВАО бутырский',
            bal: '10',
            rel: '8',
          },
          {
            subject: '',
            title: 'Безумные английские учены нашли динозавров в голове у буратино!',
            date: '28.06.2020',
            from: 'vk.com СВАО бутырский',
            bal: '10',
            rel: '8',
          },
          {
            subject: '',
            title: 'Безумные английские учены нашли динозавров в голове у буратино!',
            date: '28.06.2020',
            from: 'vk.com СВАО бутырский',
            bal: '10',
            rel: '8',
          },
          {
            subject: '',
            title: 'Безумные английские учены нашли динозавров в голове у буратино!',
            date: '28.06.2020',
            from: 'vk.com СВАО бутырский',
            bal: '10',
            rel: '8',
          },
          {
            subject: '',
            title: 'Безумные английские учены нашли динозавров в голове у буратино!',
            date: '28.06.2020',
            from: 'vk.com СВАО бутырский',
            bal: '10',
            rel: '8',
          },
          {
            subject: '',
            title: 'Безумные английские учены нашли динозавров в голове у буратино!',
            date: '28.06.2020',
            from: 'vk.com СВАО бутырский',
            bal: '10',
            rel: '8',
          },
          {
            subject: '',
            title: 'Безумные английские учены нашли динозавров в голове у буратино!',
            date: '28.06.2020',
            from: 'vk.com СВАО бутырский',
            bal: '10',
            rel: '8',
          },
          {
            subject: '',
            title: 'Безумные английские учены нашли динозавров в голове у буратино!',
            date: '28.06.2020',
            from: 'vk.com СВАО бутырский',
            bal: '10',
            rel: '8',
          },
        ],
      }
    },
    computed: {
      bg() {
        return this.background ? 'https://cdn.vuetifyjs.com/images/backgrounds/bg-2.jpg' : undefined
      },
      dateRangeText() {
        return this.date;
      },
    },
    watch: {
      date(val) {
        if (val.length > 1) {
          this.dateFormat = moment(val[0]).format('DD.MM.YYYY') + ' - ' + moment(val[1]).format('DD.MM.YYYY');
        } else {
          this.dateFormat = moment(val[0]).format('DD.MM.YYYY')
        }
      },
    },
  }
</script>
<style>
  .v-toolbar__content .v-btn.v-btn--icon.v-size--default,
  .v-toolbar__extension .v-btn.v-btn--icon.v-size--default {
    width: 25px !important;
    height: 25px !important;
    margin: auto 5px;
  }


  .v-icon {
    font-size: 13px;
  }
  .v-navigation-drawer {
    height: calc(100vh - 50px) !important;
    top: 49px !important;
    z-index: 9 !important;
  }

  .theme--dark.v-overlay {
    z-index: 9 !important;
  }

  .v-data-table-header-mobile {
    display: none;
  }

  .v-main .top {
    height: 300px;
    position: relative;
  }

  .v-main .top .hide-text {
    position: relative;
    bottom: 30px !important;
    left: 15px;
    cursor: pointer;
  }

  .v-main .top .hide-text:hover {
    text-decoration: underline;
  }

  .v-main .bottom {
    height: calc(100vh - 400px);
  }

  .v-main .bottom .text_title {
    height: 30px;
    width: 100%;
    background: #1976d2;
  }

  .v-main .bottom .text {
    margin-top: 50px;
    padding: 15px;
  }

  .left-tree {
    margin: 5px 7px;
    background: #ffffff;
    height: calc(70vh - 65px);
    /*border-bottom: 2px dotted #ffffff;*/
    /*padding: 10px 5px;*/
  }

  .left-tree-bottom {
    height: calc(30vh);
    overflow: auto;
    border: 0px;
  }

  .left-tree *,
  .left-tree button:before,
  .left-tree button:after {
    color: #000;
  }

  .v-treeview-node__root {
    min-height: 30px !important;
  }

  .v-treeview-node__label {
    font-size: 14px !important;
  }

  .v-treeview-node__level {
    width: 10px;
  }

  .search_input .v-input__slot {
    margin-top: -5px !important;
    top: 15px;
  }

  .search_input .v-input__slot input {
    padding: 3px 0;
  }

  .input-div {
    width: auto;
    display: inline-block
  }

  .input-div:not(:first-child) {
    margin-left: 10px;
  }

  .input-mobile .v-input {
    margin: 0 15px !important;
  }

  .v-menu__content {
    z-index: 10 !important;
  }

  input[type="time"] {
  }

  /* Chrome, Safari, Edge, Opera */
  input[type="time"]::-webkit-outer-spin-button,
  input[type="time"]::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
  }

  /* Firefox */
  input[type="time"] {
    -moz-appearance: textfield;
  }

  .v-data-footer__select {
    display: none !important;
  }

  @media (max-width: 600px) {
    .v-toolbar__content .v-btn.v-btn--icon.v-size--default,
    .v-toolbar__extension .v-btn.v-btn--icon.v-size--default {
      width: 30px !important;
      height: 30px !important;
      margin: auto 5px;
    }

    .v-icon {
      font-size: 14px;
    }

    .v-main .bottom {
      height: calc(100vh - 400px);
    }

    .input-mobile .col {
      margin: 0px !important;
      padding: 0px !important;
    }

    table tr th:nth-child(2) {
      min-width: 300px !important;
    }
  }

</style>
