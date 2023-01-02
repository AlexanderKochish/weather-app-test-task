<template>
  <div class="max-w-[1200px] mx-auto min-h-screen bg-black">
    <header
      class="bg-[url('../assets/1627489123_32.jpg')] w-full h-60 object-cover bg-no-repeat bg-center"
    >
      <div
        class="bg-gradient-to-t from-black via-black/70 to-black/20 w-full h-60 flex items-center justify-center"
      >
        <h1 class="text-white font-medium text-3xl">Weather</h1>
      </div>
    </header>
    <div class="flex flex-col items-center">
      <div
        class="rounded-xl -mt-10 bg-gray-900/70 w-[90%] min-h-80 p-4 text-gray-300"
      >
        <table class="table-auto w-full">
          <thead class="w-full">
            <tr
              class="w-full text-xl flex items-center justify-between border-b p-2 text-white"
            >
              <th
                class="w-1/5 inline-flex items-start self-start animate-pulse cursor-pointer hover:text-cyan-500"
                @click="sortCityes"
              >
                City
              </th>
              <th
                class="w-1/5 inline-flex items-start self-start animate-pulse cursor-pointer hover:text-cyan-500"
                @click="sortMinTemperature"
              >
                Min.temperature
              </th>
              <th
                class="w-1/5 inline-flex items-start self-start animate-pulse cursor-pointer hover:text-cyan-500"
                @click="sortMaxTemperature"
              >
                Max.temperature
              </th>
              <th
                class="w-1/5 inline-flex items-start self-start animate-pulse cursor-pointer hover:text-cyan-500"
              >
                Delete city
              </th>
            </tr>
          </thead>
          <tbody
            class="flex justify-between w-full min-h-scree py-3"
            v-for="city in cityes"
            v-bind:key="city?.latitude"
          >
            <tr
              class="text-lg flex justify-between w-full items-center p-2 bg-gray-800 rounded-sm drop-shadow-md"
            >
              <td class="w-1/5 flex flex-col">
                {{
                  city?.timezone
                    .split("/")
                    .reverse()
                    .slice(0, 1)
                    .join("")
                    .replace("_", " ")
                }}
                <span>{{
                  city?.daily.time.join(".").split("-").join(".").slice(0, 10)
                }}</span>
              </td>
              <td class="w-1/5 text-sky-500">
                {{ city?.daily.temperature_2m_min.join(", ")
                }}<span>{{ city?.daily_units.temperature_2m_min }}</span>
              </td>
              <td class="w-1/5 text-yellow-500">
                {{ city?.daily.temperature_2m_max.join(", ")
                }}<span>{{ city?.daily_units.temperature_2m_max }}</span>
              </td>
              <td class="w-1/5">
                <button
                  class="bg-red-700 text-white rounded-lg hover:bg-red-800 px-3 py-1"
                  @click="deleteCity(city?.timezone)"
                >
                  Delete
                </button>
              </td>
            </tr>
          </tbody>
        </table>
        <div>
          <button class="bg-sky-800 px-2 py-1 rounded-lg" @click="showInput">
            Select Date
          </button>
          <div v-if="show_input">
            <label class="cursor-pointer" for="select_date">
              Enter the day you are interested in and find out the weather.
            </label>
            <form @submit.prevent="selectDay(select_date)">
              <input
                class="text-black"
                id="select_date"
                type="text"
                placeholder="2023-01-01"
                v-model="this.select_date"
              />
            </form>
            <span v-if="handleError" class="text-red-600"
              >Something is wrong.Please try to enter the correct date!</span
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",

  data() {
    return {
      cityes: [],
      show_input: false,
      select_date: "2023-01-01",
      handleError: false,
    };
  },

  methods: {
    // We get the current cityes for weater.
    async getWeather() {
      const [London, Berlin, New_York, Tokyo] = await Promise.all([
        fetch(
          `https://api.open-meteo.com/v1/forecast?latitude=50.45&longitude=30.52&daily=weathercode,temperature_2m_max,temperature_2m_min&current_weather=true&timezone=Europe%2FLondon&start_date=2023-01-01&end_date=2023-01-01`
        )
          .then((res) => res.json())
          .catch((err) => console.log(err)),
        fetch(
          `https://api.open-meteo.com/v1/forecast?latitude=50.45&longitude=30.52&daily=weathercode,temperature_2m_max,temperature_2m_min&current_weather=true&timezone=Europe%2FBerlin&start_date=2023-01-01&end_date=2023-01-01`
        )
          .then((res) => res.json())
          .catch((err) => console.log(err)),
        fetch(
          "https://api.open-meteo.com/v1/forecast?latitude=50.45&longitude=30.52&daily=weathercode,temperature_2m_max,temperature_2m_min&current_weather=true&timezone=America%2FNew_York&start_date=2023-01-01&end_date=2023-01-01"
        )
          .then((res) => res.json())
          .catch((err) => console.log(err)),
        fetch(
          "https://api.open-meteo.com/v1/forecast?latitude=50.45&longitude=30.52&daily=weathercode,temperature_2m_max,temperature_2m_min&current_weather=true&timezone=Asia%2FTokyo&start_date=2023-01-01&end_date=2023-01-01"
        )
          .then((res) => res.json())
          .catch((err) => console.log(err)),
      ]);
      return (this.cityes = [
        { ...London },
        { ...Berlin },
        { ...New_York },
        { ...Tokyo },
      ]);
    },
    // Delete select city.
    deleteCity(timezone) {
      this.cityes = this.cityes.filter((c) => c.timezone !== timezone);
    },
    // Sort by city.
    sortCityes() {
      this.cityes = this.cityes.sort((a, b) =>
        a.timezone.split("/").reverse().slice(0, 1).join("").replace("_", " ") >
        b.timezone.split("/").reverse().slice(0, 1).join("").replace("_", " ")
          ? 1
          : -1
      );
    },
    // Sort by min.temperature.
    sortMinTemperature() {
      this.cityes = this.cityes.sort(
        (a, b) => a.daily.temperature_2m_min - b.daily.temperature_2m_min
      );
    },
    // Sort by max.temperature.
    sortMaxTemperature() {
      this.cityes = this.cityes.sort(
        (a, b) => a.daily.temperature_2m_max - b.daily.temperature_2m_max
      );
    },
    // Select the day on which you want to see the weather.
    async selectDay() {
      const numDate = /(\d{4})-(\d{2})-(\d{2})/;
      if (!numDate.test(String(this.select_date))) {
        this.handleError = !this.handleError;
      }
      const [London, Berlin, New_York, Tokyo] = await Promise.all([
        fetch(
          `https://api.open-meteo.com/v1/forecast?latitude=50.45&longitude=30.52&daily=weathercode,temperature_2m_max,temperature_2m_min&timezone=Europe%2FLondon&start_date=${this.select_date}&end_date=${this.select_date}`
        )
          .then((res) => res.json())
          .catch((err) => console.log(err)),
        fetch(
          `https://api.open-meteo.com/v1/forecast?latitude=50.45&longitude=30.52&daily=weathercode,temperature_2m_max,temperature_2m_min&timezone=Europe%2FBerlin&start_date=${this.select_date}&end_date=${this.select_date}`
        )
          .then((res) => res.json())
          .catch((err) => console.log(err)),
        fetch(
          `https://api.open-meteo.com/v1/forecast?latitude=50.45&longitude=30.52&daily=weathercode,temperature_2m_max,temperature_2m_min&timezone=America%2FNew_York&start_date=${this.select_date}&end_date=${this.select_date}`
        )
          .then((res) => res.json())
          .catch((err) => console.log(err)),
        fetch(
          `https://api.open-meteo.com/v1/forecast?latitude=50.45&longitude=30.52&daily=weathercode,temperature_2m_max,temperature_2m_min&timezone=Asia%2FTokyo&start_date=${this.select_date}&end_date=${this.select_date}`
        )
          .then((res) => res.json())
          .catch((err) => console.log(err)),
      ]);
      return (this.cityes = [
        { ...London },
        { ...Berlin },
        { ...New_York },
        { ...Tokyo },
      ]);
    },
    showInput() {
      this.show_input = !this.show_input;
    },
  },
  mounted() {
    this.getWeather();
    this.selectDay();
  },
};
</script>
