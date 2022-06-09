<template>
  <div>
    <h1>APIサンプル</h1>
    <h2>API実行結果</h2>
    <div>
      <p>{{ addressForDisplay }}の住所</p>
      <table class="table1">
        <thead>
          <tr>
            <th v-for="t in times" :key="t">
              {{ t }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td v-for="w in weathers" :key="w">{{ descriptionWeather(w) }}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <h2>プログラム解説</h2>
    <h3>天気予報取得API</h3>
    <ol>
      <li>{{ weatherApiUrl }}のAPIをGETします。</li>
      <li>APIの結果をJSONで取得します。</li>
      <li>{{ data }}</li>
      <li>JSONデータを整形して画面上に表示します。</li>
      <table class="table1">
        <thead>
          <tr>
            <th v-for="t in times" :key="t">
              {{ t }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td v-for="w in weathers" :key="w">{{ descriptionWeather(w) }}</td>
          </tr>
        </tbody>
      </table>
    </ol>
    <h3>住所取得API</h3>
    <ol>
      <li>
        天気予報APIが緯度経度でのデータ取得だったので、緯度経度より住所を取得するAPIを実行します。
      </li>
      <li>{{ addressApiUrl }}のAPIをGETします。</li>
      <li>APIの結果をJSONで取得します。</li>
      <li>{{ address }}</li>
      <li>JSONデータを整形して画面上に表示します。</li>
      <li>{{ addressForDisplay }}</li>
    </ol>
  </div>
</template>

<script setup lang="ts">
import { Ref } from "vue";

const weatherApiUrl =
  "https://api.open-meteo.com/v1/forecast?latitude=35.6785&longitude=139.6823&daily=weathercode&windspeed_unit=ms&timezone=Asia%2FTokyo";
const addressApiUrl =
  "http://geoapi.heartrails.com/api/json?method=searchByGeoLocation&x=139.6823&y=35.6785";
const data = ref<Object>();
const address = ref<Object>();
const times = ref<string[]>();
const weathers = ref<number[]>();
const addressForDisplay = ref<string>();

const descriptionWeather = (weatherCode: number): string => {
  if (weatherCode === 0) {
    return "晴天";
  } else if (weatherCode === 1) {
    return "主に晴れ";
  } else if (weatherCode === 2) {
    return "所により曇り";
  } else if (weatherCode === 3) {
    return "曇り";
  } else if (weatherCode === 45) {
    return "霧";
  } else if (weatherCode === 48) {
    return "堆積するライムフォグ";
  } else if (weatherCode === 51) {
    return "霧雨:軽い";
  } else if (weatherCode === 53) {
    return "霧雨:中程度の";
  } else if (weatherCode === 55) {
    return "霧雨:濃い";
  } else if (weatherCode === 56) {
    return "着氷性の霧雨:軽い";
  } else if (weatherCode === 57) {
    return "着氷性の霧雨:密度の高い";
  } else if (weatherCode === 61) {
    return "雨:わずか";
  } else if (weatherCode === 63) {
    return "雨:中程度";
  } else if (weatherCode === 65) {
    return "雨:激しい強度";
  } else if (weatherCode === 66) {
    return "着氷性の雨:軽い";
  } else if (weatherCode === 67) {
    return "着氷性の雨:重い";
  } else if (weatherCode === 71) {
    return "降雪:わずか";
  } else if (weatherCode === 73) {
    return "降雪:中程度";
  } else if (weatherCode === 75) {
    return "降雪:重い";
  } else if (weatherCode === 77) {
    return "雪粒";
  } else if (weatherCode === 80) {
    return "にわか雨:わずか";
  } else if (weatherCode === 81) {
    return "にわか雨:中程度";
  } else if (weatherCode === 82) {
    return "にわか雨:激しい";
  } else if (weatherCode === 85) {
    return "にわか雪:わずか";
  } else if (weatherCode === 86) {
    return "にわか雪:重い";
  } else if (weatherCode === 95) {
    return "雷雨:軽度または中程度";
  } else if (weatherCode === 96) {
    return "わずかなひょう";
  } else if (weatherCode === 99) {
    return "激しいひょうを伴う雷雨";
  } else {
    return `不明な天候: 天候コード=${weatherCode}`;
  }
};
onMounted(async () => {
  const fetchData = await $fetch(weatherApiUrl);
  data.value = fetchData;
  times.value = fetchData.daily.time;
  weathers.value = fetchData.daily.weathercode;

  const longitude = data.value.longitude;
  const latitude = data.value.latitude;

  const addressResult = await $fetch(addressApiUrl);
  address.value = addressResult;
  const targetLocation = addressResult.response.location[0];
  addressForDisplay.value = `${targetLocation.prefecture} ${targetLocation.city} ${targetLocation.town}`;
});
</script>

<style scoped>
.table1 {
  border: 1px solid gray;
}
.table1 th,
.table1 td {
  border: 1px solid gray;
}
</style>
