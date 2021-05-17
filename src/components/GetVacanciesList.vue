<template>
    <label for="search" class="block mb-1">Введите название вакансии</label>
    <input id="search" type="text" @change="change_filter" v-model="query" class="p-2 focus:outline-none focus:ring-2 focus:ring-indigo-300 text-center border rounded border-indigo-500" autofocus/>
    <div class="flex flex-row justify-center w-full">
      <div v-for="b in buttons" :key="b" >
        <button class="select-none hover:bg-indigo-100 p-1 px-4 rounded border-l border-r border-indigo-500 mx-1 mt-1 mb-5 bg-indigo-50 text-sm" @click="query = b "> {{ b }} </button>
      </div>
    </div>
    <div class="m-10 mt-2">
      <p class="select-none p-2 border bg-indigo-50" @click="show_map = !show_map">{{ (show_map == true) ? 'Скрыть карту' : 'Показать карту' }}</p>
      <div v-if="info.length > 0">
        <div v-show="show_map">
          <yandex-map :info="info" />
        </div>
      </div>
    </div>
    <div v-if="info.length > 0" class="w-full flex items-center">
        <div class="m-5 w-full border border-b-0 rounded border-indigo-500" v-if="info">
            <div class="text-center flex flex-row justify-between border-b  border-indigo-500">
                <div class="w-2/5 p-3 ml-2 border-r border-indigo-500">Название вакансии</div>
                <div class="w-2/5 p-3 ml-2 border-r border-indigo-500">Адрес</div>
                <div class="w-1/5 p-3 ml-2">Как добраться</div>
            </div>
            <div class="text-left flex flex-row justify-between border-b  border-indigo-500" v-for="vac_info in info" :key="vac_info.id">
                <div class="w-2/5 py-2 ml-2 border-r border-indigo-500"> {{ vac_info.title }} </div>
                <div class="w-2/5 py-2 ml-2 border-r border-indigo-500"> {{ vac_info.contact_address }} </div>
                <div class="w-1/5 py-2 ml-2"> {{ (vac_info.title.indexOf('м.') > -1) ? get_metro(vac_info.title) : vac_info.contact_address }} </div>
            </div>
        </div>
    </div>
    <div v-else class="mb-10">
        <p>Извините, ничего не найдено</p>
    </div>
</template>

<script>
import YandexMap from '@/components/YandexMap'
export default {
  components: {
    YandexMap,
  },

  data() {
    return {
      info: [],
      original_info: [],
      query: 'Консультант',
      show_map: false,
      buttons: [
        'Директор',
        'Управляющий',
        'Старший продавец',
        'Администратор',
        'Продавец',
        'Консультант',
        'Кассир',
      ],
    }
  },
  watch: {
    query: function() {
    //   this.query = this.query.toLowerCase();
      this.change_filter();
    }
  },

  mounted() {
    const axios = require('axios');
    axios({
      method: 'POST',
      url: 'https://job.chitai-gorod.ru/api/vacancy/index?pageSize=1000',
      data: {
        cities_id: 1,
        vacancy_category_id: 2,
      },
    }).then((result) => {
      this.original_info = result.data.data
      this.info = this.original_info
      this.info = []
      for (let index = 0; index < this.original_info.length; index++) {
        if (this.original_info[index].title.toLowerCase().indexOf(this.query.toLowerCase()) > -1) {
          this.info.push(this.original_info[index])
        }
      }
    }).catch((err) => {
      console.log(err)
    });
  },

  methods: {
    change_filter: function() {
      this.info = []
      for (let index = 0; index < this.original_info.length; index++) {
        if (this.original_info[index].title.toLowerCase().indexOf(this.query.toLowerCase()) > -1) {
          this.info.push(this.original_info[index])
        }
      }
    },
    get_metro: function(value) {
      const regex = /м\.\s?[А-яёЁ\s-.]+/gm;
      let matches;
      return ((matches = regex.exec(value)) !== null) ? matches[0] : ''
    }
  },

}
</script>