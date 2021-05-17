
<template>
  <div class="flex w-full justify-center">
    <div id="map" style="width: 1000px; height: 700px" class="mt-2"></div>
  </div>
</template>


<script>
  export default {
    props: ['info',],

    data() {
      return {
        coordinates: []
      }
    },

    mounted() {
      /* eslint-disable no-undef */
      
      ymaps.ready(() => {
        // eslint-disable-next-line no-unused-vars
        var myMap = new ymaps.Map("map", {
          center: [55.76, 37.64],
          zoom: 12
        });
        this.info.forEach(element => {
          ymaps.geocode(element.contact_address, {
            results: 1
          }).then((res) => {
            // Выбираем первый результат геокодирования.
            var obj = res.geoObjects.get(0),
            coords = obj.geometry.getCoordinates();
            this.coordinates.push(coords)
            
            obj.options.set('preset', 'islands#darkBlueDotIconWithCaption');
            // Получаем строку с адресом и выводим в иконке геообъекта.
            obj.properties.set('iconCaption', element.title);

            // Добавляем первый найденный геообъект на карту.
            // myMap.geoObjects.add(obj);


            var myPlacemark = new ymaps.Placemark(coords, {
                balloonContentHeader: element.title,
                balloonContentBody: element.contact_address,
                balloonContentFooter: '<a target="_blank" href="https://job.chitai-gorod.ru/vacancy-single/'+element.id+'/">Ссылка на вакансию</a>',
              });

            myMap.geoObjects.add(myPlacemark);
          });
        });
      })
    },

    methods: {
      dump: function(info) {
        console.log(info)
      }
    }
  }
</script>
