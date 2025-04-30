<template>
  <div id="app" class="centered-container">
    <div v-if="!isDobSet">
      <form>
        <h1 id="dob" class="age-label">When were you born?</h1>
        <footer>
          <input id="dob" v-model="dob" type="date" name="dob" />
          <button @click.prevent="save">Start</button>
        </footer>
      </form>
    </div>
    <div v-else>
      <div class="countdown">
        <div class="time-section">
          <span class="time">{{ years }}</span>
          <div class="label">YEARS</div>
        </div>
        <div class="time-section">
          <span class="time">{{ months }}</span>
          <div class="label">MONTHS</div>
        </div>
        <div class="time-section">
          <span class="time">{{ days }}</span>
          <div class="label">DAYS</div>
        </div>
        <div class="time-section">
          <span class="time">{{ hours }}</span>
          <div class="label">HOURS</div>
        </div>
        <div class="time-section">
          <span class="time">{{ minutes }}</span>
          <div class="label">MINUTES</div>
        </div>
        <div class="time-section">
          <span class="time">{{ seconds }}</span>
          <div class="label">SECONDS</div>
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
      dob: "",
      isDobSet: false,
      years: 0,
      months: 0,
      days: 0,
      hours: 0,
      minutes: 0,
      seconds: 0,
      timer: null,
      totalDots: 4160, // 80 years * 52 weeks
    };
  },
  computed: {
    filledDots() {
      if (!this.dob) return 0;
      const now = new Date();
      const birthDate = new Date(this.dob);
      const diff = now - birthDate;
      const weeks = Math.floor(diff / (1000 * 60 * 60 * 24 * 7));
      return weeks;
    },
  },
  mounted() {
    if (localStorage.dob) {
      this.dob = new Date(localStorage.dob).toISOString().split("T")[0];
      this.isDobSet = true;
      this.startTimer();
    }
  },
  beforeDestroy() {
    if (this.timer) clearInterval(this.timer);
  },
  methods: {
    save() {
      if (!this.dob) return;

      localStorage.dob = new Date(this.dob).toISOString();
      this.isDobSet = true;
      this.startTimer();
    },
    startTimer() {
      this.updateAge();
      if (this.timer) clearInterval(this.timer);
      this.timer = setInterval(this.updateAge, 1000);
    },
    updateAge() {
      const now = new Date();
      const birthDate = new Date(this.dob);

      // Calculate years and months
      let years = now.getFullYear() - birthDate.getFullYear();
      let months = now.getMonth() - birthDate.getMonth();

      // Calculate days
      let days = now.getDate() - birthDate.getDate();

      // Adjust months and years if days are negative
      if (days < 0) {
        // Get the last day of the previous month
        const lastMonth = new Date(now.getFullYear(), now.getMonth(), 0);
        days += lastMonth.getDate();
        months--;
      }

      // Adjust years if months are negative
      if (months < 0) {
        years--;
        months += 12;
      }

      // Calculate time components
      const hours = now.getHours();
      const minutes = now.getMinutes();
      const seconds = now.getSeconds();

      this.years = years;
      this.months = months;
      this.days = days;
      this.hours = hours;
      this.minutes = minutes;
      this.seconds = seconds;
    },
    getDotStyle(i) {
      if (i <= this.filledDots) {
        const hue = (i / this.totalDots) * 240;
        return { background: `hsl(${hue}, 80%, 50%)` };
      }
      return { background: "#333" };
    },
  },
};
</script>

<style>
html,
body {
  height: 100%;
  margin: 0;
  padding: 0;
  background: #222;
}

#app {
  min-height: 100vh;
  min-width: 100vw;
  background: #222;
}

.centered-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background: #222;
}

.content-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  flex: 1;
  width: 100vw;
  height: 100vh;
}

.progress-grid {
  display: grid;
  grid-template-columns: repeat(52, 20px); /* 52 weeks per row, larger dots */
  gap: 6px;
  max-width: 1100px;
  margin-bottom: 40px;
  margin-top: 20px;
  justify-content: center;
}

.dot {
  width: 18px;
  height: 18px;
  border-radius: 50%;
  background: #333;
  transition: background 0.3s;
}

.countdown {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 2.5rem;
  margin-bottom: 0;
}

.time-section {
  text-align: center;
}

.time {
  font-size: 3rem;
  font-weight: bold;
  color: #b0b5b9;
}

.label {
  font-size: 1.2rem;
  color: #494949;
  margin-top: 0.3rem;
}

@media (prefers-color-scheme: dark) {
  .time {
    color: #b0b5b9;
  }
  .label {
    color: #494949;
  }
}
</style>
