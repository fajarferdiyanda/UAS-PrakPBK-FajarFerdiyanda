<template>
    <div class="centered-content">
      <div class="weather-widget">
        <div class="widget-header">
          <h2 class="widget-title">Informasi Cuaca</h2>
        </div>
        <q-input
          filled
          v-model="searchQuery"
          label="Masukkan Nama Kota"
          class="location-input"
        />
        <q-btn @click="searchWeather" class="search-button">Cari</q-btn>
        <div v-if="loading" class="loading-message">Loading data...</div>
        <div v-else-if="weatherData" class="weather-result">
          <div class="weather-info">
            <p class="city-name">{{ weatherData.name }}</p>
            <p class="temperature">{{ weatherData.main.temp }}°C</p>
            <img
              :src="getWeatherIconUrl(weatherData.weather[0].icon)"
              :alt="weatherData.weather[0].description"
              class="weather-icon"
            />
            <p class="weather-description">
              {{ weatherData.weather[0].description }}
            </p>
          </div>
          <div class="additional-info">
            <p>Kelembaban: {{ weatherData.main.humidity }}%</p>
            <p>Kecepatan Angin: {{ weatherData.wind.speed }} m/s</p>
          </div>
        </div>
        <div v-else-if="error" class="error-message">{{ error }}</div>
      </div>
    </div>
  </template>
  
  <script>
  import { ref } from "vue";
  import axios from "axios";
  import { QInput, QBtn } from "quasar";
  
  export default {
    components: { QInput, QBtn },
    setup() {
      const searchQuery = ref("");
      const weatherData = ref(null);
      const loading = ref(false);
      const error = ref(null);
  
      const searchWeather = async () => {
        loading.value = true;
        error.value = null;
  
        try {
          const response = await axios.get(
            `https://api.openweathermap.org/data/2.5/weather?q=${searchQuery.value}&appid=bcbc6cc1860d02bd1f4306314c08d7e0&units=metric`
          );
          if (response.data.cod !== 200) {
            throw new Error("City not found");
          }
          weatherData.value = response.data;
        } catch (error) {
          console.error(error);
          error.value =
            "Gagal mengambil data cuaca atau kota tidak ditemukan";
        } finally {
          loading.value = false;
        }
      };
  
      const getWeatherIconUrl = (iconCode) => {
        return `https://openweathermap.org/img/wn/${iconCode}.png`;
      };
  
      return {
        searchQuery,
        weatherData,
        loading,
        error,
        searchWeather,
        getWeatherIconUrl,
      };
    },
  };
  </script>
  
  <style scoped>
  .centered-content {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: calc(100vh - 60px); /* Mengurangi tinggi navbar dari 100vh */
    margin-top: 90px; /* Jarak dari atas halaman */
  }
  
  .weather-widget {
    width: 400px; /* Ubah nilai width sesuai dengan kebutuhan */
    padding: 20px;
    border-radius: 10px;
    background-color: #f0f0f032;
    box-shadow: 0px 0px 20px 0px rgba(0, 0, 0, 0.2);
    background-image: url("data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wCEAAkGBxAQEBAQEBMQDw8PDw0ODxAPEA8PEA8NFRUWFhURFRUYHSggGBolGxUVITEhJSsrLi4uFx8zODMsNygtLisBCgoKDg0OGhAQGi0fHSAtLSstLS0tLS0rLS0tLSstLSstLS0tLSstLS0tLS0rLS0tLS0tLSsrLS0rKy0tLS0rLf/AABEIALcBEwMBIgACEQEDEQH/xAAaAAABBQEAAAAAAAAAAAAAAAAEAAECAwUG/8QAOBAAAgECBAUBBgQFBAMAAAAAAAECAxEEEhMhBTFBUWFxFCJSgZHRMqGxwQZCYoLhFZKi8CNTcv/EABoBAAMBAQEBAAAAAAAAAAAAAAABAgMEBQb/xAAkEQACAgICAgICAwAAAAAAAAAAAQIRAxIhMUFREyJhcQQUMv/aAAwDAQACEQMRAD8A6CJZEqiWRPePnmWxLYOxTFlkQIYZBkwejIIRDEOIQhAIQhAAhCEACEhDgAh0MSQikOhxhxFoQ4hCAQhDAA4hhAAhCGGA4hhAA5CRIjJgAPWB5F1SW5TItAVyISJSIMYyA4whjKI1EXRYFFlsJWLohhkWWRZRCdy2LJJLosJp1LgaZZFiaJDRA8ZslnZNCsuEU52LMwoLLhFFxKTCgsvHKo1O5ahDQ46GRJCZaHQ4hElCEIQAMTjDq9kPZR3fPoiO75isukuyV4rpcWp4Q6gSUBcD+3gr1O6Qvdfgm6ZCUB8A78kZQaIE1JryuxGola65DIavlEWymrPoNOr2KGy0iUJshJibItjKohJlcmTkyqTGOhCIXEMYGmTTKEyxM0JaCKc7BMWAphFKWxLJoKTJpg6kWKQiWi9SLFIHUiakImi9McpUiakAqJiGuIQhyUJWIjoBoIRJFVJ9C5EM1iOIQiRjE6a6voQLKnJL5gyo+yK3dy2KIRQTRgRJ0aQjY0KRaqITSpBMaJzyyHZHEZkqJTUpmvOiB16Y45BTxGRWuDRnZ78nzDsREzqx1xdo4pxcXZGqrMqbLJyvFPtsDtlolxpknIi2RbItjGkKTB6lTsKpMpkxodCuIhcQwB0yxMHUiakWKghMuoyBFIsjMCaDkyakDRmTUiRUEKRNSB1IkpCFQSpElIGUiamBNBCkSUgZSJKYhUEqRJSBs5JSAVBUZBEJXAFMmpkNGiQdcVwVVmPrE0VQSiVWW4Iqwqs/eFXJSX1DKUjQw5l4ZmpQMcjOrCjRoRC4xA6EgyMjhmehCiNSJn4mJoVJ7GZi6hWOxToy8UZeIDsXUMfE1H3PQxnn5UTg/dl8mUNkqFKpKMnGM5LbdRk0CTk1s7p9U7pmy7Zi1wi6UyqdQ0uGcGlUSnUbhB7pL8Ul38I26XDqEFtTi/Mlnf5mcs0Yuuy4wZxjZXKR21bAUJbOnD+2Ki/qjD4pwFxTnRbklu4PeVv6X19BxzRf4E40YWYRXmEbE0aXBuDKaVSrdRe8YLZyXdvojoKVKnBWjCEV4iv1KqlXoQ1Dnk3Lsxuy2vgqNRe9CKfxRSjJfNfuc5xLASoSS/FCV8ku/h+ToFUIcQpqpRqRfNRc4+JR3/x8xwk4v8DTOZhUsXRqoGw1OVSUYR3lJ2X39DpKHB6MF796kurbcVfwkbzmo9jZjqojVwvCZyV5tU12avL6dArD4OjTnngne2ybzKL7q/UJdYxlkb/yAP8A6ND45X9Fb6AeK4XVgrxaqRXwq0l/b9jUVQnCsQpyQUmcvqvuSVV9zR45hFbWhtulUS88pfXmYqkdMWpKyGgyNYtjUM/MTjUsOgo0FMmpgkKlyamQ0WkF5xs5QpCzCLovzllWWyfyBM5ZSne8X15eomVFeDQwdVXNqnVVjlYVHF2DqWMMMmOzTHKjoIVwiOJOejiyXthi8R1xmbVXFGdia9wSeLA6+JHHGKUx8VWCeE4GLWrUV7t5IvdbfzNdQHB4d15SV1GMUnKVr8+SS78zcoq0YwTvGEYxvyvZWuaTdKkYpbMLlVtH1AsTRp1LZ4qTi00+u3T08DVa12Q1DKKoJtN0EuoM5g+ci6g6EXuZHUB3UIOoUkZSKsRwahUlKbzJyd2ouyv1dhFmsIu5ezOzN1iSqGcqpONU21M6NBVCyFS6kr2vGSTfdqxnxqFikS0PUfhnDdCedzjNZJRWzTUnbf6XDJVbgucWYHy7YUwrUH1AXMNnCgoMVQfUA9QfUChB2dSjKD5SjKP1XMy/9DnbapBvt7yX1L9QlGuwTcehmLiaE6css1lfTqmu6fUrzHQ4mKrU5Qf4knKD7TX35HN16U6byzi4vnZ9V3T6m8J332BdCpYIhUuBKlPLnyzyfFlllt3uNGoN8lI0VMlmAoV+5Yqq7kNGiQTnGzlGdDaiEOg5TUue0ujK3UceYG63YeNe9k1m6Lv8hVRfD77C/aiSxZrYThVGmlKdpVPhlvGHi3V+QmUk9rU3Hs4xt9LGLyx8ItQkYtBVKrtTTk1z3SS9W9grC8NlneussIpOyknnfa6fINpqME1DLBN5mlfna37EJVo9W5foS5t9D0fkIhlW0IxhHq4pK/3I1K65L6gc8S34RZgqLqzUVt1k/hj3J18sL8RJqTbst32RY6NTnkn/ALZG5h6UKatFW7vq/VluoZPL6RosPtnMOZB1DocZhYVVvtLpJc1690ctiU4SlCW0ouz+6NISUjLJBxLXUISqA0qhW6psomDC9QQLFye6Umu6TaGHROrKeNYXSqe7tConKPh9Y/8Ae6BYzOhxlOnWUVNtZG2srSbuuX6fQqWCw3wX9Zz+445OKYNpGNroXtBqVeE4eX4XKm/Esy+af3MjiHD6lHd2lDpOPL5roXFxYuyxYgkq5mqqSVUvUNTTVcmqpl6pJVidBUzSciOoBxrknUCgqwvUH1AHVH1goVB8KzRbXnCqoKp/JNSXLddYvw9voZmqLVDUKN+nj7vx28djnuIYGcJTmoPSzScWmmlG+17cl6k1WL6WOcf0+QknHoFwCYLA1au8Y+78cnlj9evyNGHBPiqxT7Ri5L6tojLiEnt0WyS6Ih7S+4NyZrFls+Bz/knCfh3g/l0/MzcRSnTeWcXF9E+q7p9TRhimupfLGxnHJUSnHnv0fdPoQpSXfJogDA8MqVVm2hDpKd9/RdTQwvC40pxqOopqDzZcmXe23V9bDV8c5ctktklskuwPKsxNyY6Dq+IuyvVA9QbUChBjqDagJqmjwbBazcp3VOD3ts5S+FfuJ0lbGk2yqLcnaKcn2Sbf5G/wKDhTlKScZSlbdNPKltz9WXxrRgssEoxXRKyISxFznnNyVUbwgouwt1BtQCdcg65Gpo5B+qYX8UWTpz+KMov+21v1DHXKMZSp1lHO5e7mtlaXO3O68FwWsrMpytUDcFwMZrVqbxu1CPR25t+PBrzVO2XJDL2yRsA0nGnCNOLbUb2btfdt7/UZ1i5XJ2ZJpGhTrKKUY2jFKyS5JCM7WELQNzCeLHWJMVYgksQd/wAZhqbccUGYbEZlaVnFqzT3TXY5+hVv6BXtNjOcPAaEsVwWSqWopSptZk3JLL/S2+ZCpweultFT8U5KT+nN/IsWOfc1eGYlpKT5vl4REpSii4KUnRgQw3xbeOq9ScsHDo5fVfY2f4ijGUFXjtJNRqW/mT2UvW+3zOeWKHGTkrOjSK4GrYeUeXvL8ymFcL1rkKfDnVbmmoQi0qkrXSb5WXVmil7M8mNJWhYehUqtqnFzaV3aySXlvZFWIjOnLLNOMl0fbv5N3BVKdCDjBuV3mbla97Wtt0+5Cviqc2nUhGbSsnJXsuwlJ31wc7fkwtUWsbFTC4aqrKOnLpKG1v7eTMDG0ZUpuEvVNcpR6NFxaY1TCNUWqAao+oVQ9Q9Viarmbqj6otR0airktcylWJKsS4lI1FXH1jMVYkqxOpojR1haoPgaE60ssOivKTdoxXds2KPCIwlCUqsZKMoylHI7SS3te/7EtpBwPQ4TXnFStGKe6U5ZW16dPmbOEelRhB7SWZyV0/ebfVeLAeI4jduzBZYvyYtSl2UnRq+0EXXMp4ojLFB8YORqOuRdcyniiKxDk0ldt7JLdt+CvjIbZqPEEXiCMOEYhq+WMfEpq/5AGMp1aTtUi435PZp+jWwJRfCZNMPeIHpTlN2gnKXaKb/QXBuGOtapUvGn0S2lU9OyOmo5ILLBKKXRbfPyROajwuSlD2Ya4difg+soL9xG97QIy+SXovXGccqGH/8AVT/2opr8Lw8/w3py7xba+j/wBLEllPE7r1PU0aOemC43B1MPbNZxf4Zx/C/Hh+AP2k6mrVjODhNXjJWa/f1Obr8FqJNxcZpXsk2pOPpa1/BEXfZV+GVPEm68TZWXTY491DYpYjNFPut/XqE4Wb4l2a1XGxlTlTm3lllva19mn19AGNDDvZOpHzmT/KwsXXpypwUYtTV8zvzAoLcmK4FODvg6bhFanh42jaU23epaztfZLsrDcU4jmpzSsrpydkleXd+dkY1GdiHE8Ram+8vdX7magtrNEvq7K6eK3FPEGXCqWVKp068nMoh8MVYuxOXERinLLKLdnbNdPpz8IxtUlGvbkPUnUhiYypycJWzLtumu6KtU0qfEpR9eV/ApYmnJqU4RlJcm1z9V1+Y6GZ2qNqmjOpRbUnCN10W0X6pbEvaqT2dOnb/4iv2CgszNY0sJgJSWao9OPNXV5tenT5lcIUYz1F0V1B7pS7/4GrY1yfMKD9GnHDYZc88vLkl+iI1MDRl+Ccov+q0l+VmZGsSVcjQas3sJX0KWS6zuUpSad097Lf0S+pW8a317mLrjqsLQpI1/aiPtRla42sGhRqvFDe0mVrDawaBRpvEnWfwzRjCkqr/HUvZ/DT5JL1tf6HAOsdhw3Fv2ele6aglZ7bLZP58yMkLVEyR0rxaKcTVp1IuE0pRfNP8AXwYjxZB4sxWAk33jUtlslsktkl2IPHGBLFlcsWWv44HQf6ghHOe1/wDbiK/roKMJYglHEGQq5JVz0XEvU6dYnYeOLsYeGxe1ix1zn0pilE2MDTwkZTlUpuWZS5Te0n1s9jJq0HRblF56Ld38UPLXVENclHFNAsatsE2jX4ZwmdeKneMKb5Se7fpH72NaH8O0OtSpfulBL6Wf6nLQx0k7qTT8NovXFavxy+pjPFNvhm3yryjZx/BNOOaFSM18Elkm/Tez/I47iOKcpWd42usr2a9fJrxxzvdtt927szOM2qp1V+KP4v6o/dF48bXfJLm2BRqFk6pnwmWVKpT7EkEao+oBagtUoKDVUFqlOFpSqPLHkt23yiu7NWnhKMed5vu20vohqNkvgA1BapoTw1CXJOD7xk3+TMvGUJU3vvF/hkuT+zBqgXJZqi1QPUFqCHQXqi1QPUHc/l6gOgvVHVUCzmjwqhF/+Se8VdRi+TfVvwCVi6Ho0qk94xlJd0nb6irU5w/FGUV3advqaMseMsf33T5p8mVoK2B4Cg6srJ2it5S7Lt6m3SoUIK2RSfefvN/sZkMTGCaglFNuTS7kHig0B8mrUo0JWeRJxafu7Jrs1ysWzxlzE9pGeJD4wo2HiiLxRjvEkXiR6Do1niiDxJlPEEHiC1AKNX2kRk64h6hRnqqS1QHUHVULNaD4V7MLp4lMxtUkqpLVhRtagtQyo4tk1jCKYtEaaqElVsZbxhXLENhTDU06uKvshqNdbp8mmn6MzNUlGoUuBOJoU+GJq8Z3klykrJv16GbVvFtPZp2a8hlLEZSriEJVJqUVe8Ypvzuv0sTrfKGuOwaF27JNt9FuwhYKr8P/ACjf9S6NSNJZY8/5pdW/sVPFsrRLsXIdQlp01H+Z+9L1fQg6wC69xtUdi1D1WLnJVISg+114kuRlaoRhMRZggaAcxfg6OpK3JLeT7L7ldTCTilylyXu3e/oFYa9OEs20pNdU9kvHqyYw55Gw+NaFPaCS89X6saWLUtpJSXZq5lSrDapewtTTo1YU75Fa7u3zfpfsRnim/wAzO1RKt+4rDUN1RtUC1RtULHqG6ozqgWqNqhYahuqM6oHqjag7HQZqjaoHqjaoWFBbqjOqCao2oOx0F6ogPUEFjoHmyGoIRimyh9QfUEILAfUH1BCCwFqD6ghBYElULYTtuOITYqK51h1iX3EIadDZF1bi1BhBYh9QWqIQWAtUfVGEFgWRxUl1IyxDfMcQ9mFENQWqMIVgLUHVT9xhBYDagtQQgsBtQWoIQWMbUG1BxBYDag2oIQWAzqDaghDsBZxhCFYz/9k="); /* Ganti dengan URL gambar latar belakang yang valid */
    background-size: cover;
    background-position: center;
    text-align: center; /* Memastikan konten di tengah secara vertikal */
  }
  
  .widget-header {
    text-align: center;
    margin-bottom: 15px;
  }
  
  .widget-title {
    font-size: 1.8rem;
    color: #090909a0;
    font-family:'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif
  }
  
  .location-input {
    width: calc(100% - 10px); /* Sesuaikan nilai width untuk menyesuaikan dengan tombol */
    padding: 12px;
    border: 1px solid #0a0a0a;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    margin: center;
    background-color: hwb(0 100% 0% / 0.34);
  }
  
  .search-button {
    width: 110px; /* Ubah nilai width sesuai dengan kebutuhan */
    padding: 12px 25px;
    background-color: #4caf50;
    color: #fff;
    border: none;
    border-radius: 7px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    cursor: pointer;
    transition: background-color 0.3s ease;
    margin-top: 8px;
  }
  
  .search-button:hover {
    background-color: #45a049;
  }
  
  .loading-message {
    font-style: italic;
    color: #121212;
    margin-top: 10px;
  }
  
  .weather-result {
    margin-top: 20px;
    padding: 15px;
    border-radius: 5px;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
    transition: box-shadow 0.3s ease, transform 0.3s ease;
    background-color: hwb(0 100% 0% / 0.28);
  }
  
  .weather-result:hover {
    box-shadow: 0px 0px 15px rgba(0, 0, 0, 0.2);
    transform: translateY(-5px);
  }
  
  .weather-info {
    margin-bottom: 10px;
    text-align: center;
  }
  
  .city-name {
    font-weight: bold;
    color: #1b1919;
    font-size: 1.5rem;
  }
  
  .temperature {
    font-size: 3rem;
    color: #0e0606;
  }
  
  .weather-icon {
    width: 100px;
    height: 100px;
    margin: 0 auto;
    display: block;
  }
  
  .weather-description {
    font-style: italic;
    color: #080303;
  }
  
  .additional-info {
    margin-top: 10px;
  }
  
  .additional-info p {
    margin: 5px 0;
    color:#080303
  }
  
  .error-message {
    color: red;
    margin-top: 10px;
    text-align: center;
  }

  
  </style>
  