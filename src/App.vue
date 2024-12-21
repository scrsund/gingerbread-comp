<template>
  <div class="winter-background">
    <div class="snowflakes" aria-hidden="true">
      <div
        v-for="snowflake in snowflakes"
        :key="snowflake.id"
        class="snowflake"
        :style="snowflake.style"
        @animationend="removeSnowflake(snowflake.id)"
      >
        {{ snowflake.char }}
      </div>
    </div>
    <div class="content">
      <div class="gingerbread-text">Gingerbread House Competition</div>
      <div class="year-text">2024</div>

      <div class="theme-section">
        <div class="theme-text">Theme: Countries</div>
      </div>

      <div class="judges-container">
        <ScoreCard
          v-for="(judgeScores, judgeName) in judgesScores"
          :key="judgeName"
          :judge-name="judgeName"
          :scores="judgeScores"
        />
        <OverallResults :judges-scores="judgesScores" />
      </div>
    </div>
  </div>
</template>

<script>
import ScoreCard from "./components/ScoreCard.vue";
import OverallResults from "./components/OverallResults.vue";

export default {
  name: "App",
  components: {
    ScoreCard,
    OverallResults,
  },
  data() {
    return {
      snowflakes: [],
      nextId: 0,
      columns: 18,
      columnStatus: {},
      baseSpeed: 20,
      generationInterval: null,
      isGenerating: false,
      judgesScores: {
        Keely: {
          Technical: { "1st": "Brazil", "2nd": "Switzerland", "3rd": "Egypt" },
          Creativity: { "1st": "Egypt", "2nd": "Brazil", "3rd": "Switzerland" },
          Appearance: { "1st": "Switzerland", "2nd": "Egypt", "3rd": "Brazil" },
          "Best Overall": {
            "1st": "Switzerland",
            "2nd": "Brazil",
            "3rd": "Egypt",
          },
        },
        Ethan: {
          Technical: { "1st": "Brazil", "2nd": "Switzerland", "3rd": "Egypt" },
          Creativity: { "1st": "Switzerland", "2nd": "Egypt", "3rd": "Brazil" },
          Appearance: { "1st": "Switzerland", "2nd": "Egypt", "3rd": "Brazil" },
          "Best Overall": {
            "1st": "Switzerland",
            "2nd": "Brazil",
            "3rd": "Egypt",
          },
        },
        Kattis: {
          Technical: { "1st": "Switzerland", "2nd": "Brazil", "3rd": "Egypt" },
          Creativity: { "1st": "Brazil", "2nd": "Switzerland", "3rd": "Egypt" },
          Appearance: { "1st": "Egypt", "2nd": "Brazil", "3rd": "Switzerland" },
          "Best Overall": {
            "1st": "Egypt",
            "2nd": "Switzerland",
            "3rd": "Brazil",
          },
        },
        Fegge: {
          Technical: { "1st": "Egypt", "2nd": "Switzerland", "3rd": "Brazil" },
          Creativity: { "1st": "Brazil", "2nd": "Switzerland", "3rd": "Egypt" },
          Appearance: { "1st": "Egypt", "2nd": "Switzerland", "3rd": "Brazil" },
          "Best Overall": {
            "1st": "Egypt",
            "2nd": "Switzerland",
            "3rd": "Brazil",
          },
        },
      },
    };
  },
  methods: {
    getAvailableColumn() {
      const now = Date.now();
      const availableColumns = Array.from({ length: this.columns })
        .map((_, i) => i)
        .filter((col) => {
          const lastSpawn = this.columnStatus[col] || 0;
          return now - lastSpawn > 2500;
        });

      if (availableColumns.length === 0) return null;
      return availableColumns[
        Math.floor(Math.random() * availableColumns.length)
      ];
    },
    createSnowflake() {
      const column = this.getAvailableColumn();
      if (column === null) return null;

      const id = this.nextId++;
      const columnWidth = 98 / this.columns;
      const basePosition = column * columnWidth;
      const offset = Math.random() * (columnWidth * 0.6);
      const position = basePosition + offset;

      const speedVariation = Math.random() * 4 - 2;
      const duration = this.baseSpeed + speedVariation;

      const style = {
        "--fall-duration": `${duration}s`,
        left: `${position}%`,
      };

      this.columnStatus[column] = Date.now();

      return {
        id,
        style,
        char: id % 2 === 0 ? "❅" : "❆",
        column,
      };
    },
    addSnowflake() {
      if (this.isGenerating) return;

      const snowflake = this.createSnowflake();
      if (snowflake) {
        this.snowflakes.push(snowflake);
        this.isGenerating = true;

        setTimeout(() => {
          this.isGenerating = false;
        }, 500);
      }
    },
    removeSnowflake(id) {
      const index = this.snowflakes.findIndex((s) => s.id === id);
      if (index !== -1) {
        this.snowflakes.splice(index, 1);
        setTimeout(() => {
          this.addSnowflake();
        }, 500);
      }
    },
    startSnowfall() {
      for (let i = 0; i < this.columns; i++) {
        setTimeout(() => {
          this.addSnowflake();
        }, i * 500);
      }

      this.generationInterval = setInterval(() => {
        if (this.snowflakes.length < this.columns * 1.2) {
          this.addSnowflake();
        }
      }, 1000);
    },
    stopSnowfall() {
      if (this.generationInterval) {
        clearInterval(this.generationInterval);
      }
    },
  },
  mounted() {
    this.startSnowfall();
  },
  beforeUnmount() {
    this.stopSnowfall();
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

@import url("https://fonts.googleapis.com/css2?family=Fredoka:wght@300;400;500;600;700&display=swap");

.winter-background {
  min-height: 100vh;
  background: linear-gradient(135deg, #a8c5e8 0%, #c7ddf5 100%);
  position: relative;
  overflow: hidden;
  padding: 20px;
}

.content {
  position: relative;
  z-index: 2;
  padding-top: 40px;
}

.gingerbread-text {
  font-family: "Fredoka", sans-serif;
  font-size: 4.2em;
  background: linear-gradient(45deg, #8b4513, #a0522d);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
  letter-spacing: 1px;
  margin-bottom: 20px;
  transition: transform 0.3s ease;
  font-weight: 700;
  -webkit-text-stroke: 2px #8b4513;
}

@media screen and (max-width: 768px) {
  .gingerbread-text {
    font-size: 3.2em;
    -webkit-text-stroke: 1.5px #8b4513;
  }

  .year-text {
    font-size: 2.8em;
  }

  .theme-text {
    font-size: 1.6em;
  }
}

@media screen and (max-width: 480px) {
  .gingerbread-text {
    font-size: 2.8em;
    -webkit-text-stroke: 1.2px #8b4513;
    margin-bottom: 15px;
  }

  .year-text {
    font-size: 2.2em;
    -webkit-text-stroke: 1.2px #ff0000;
  }

  .theme-text {
    font-size: 1.5em;
  }
}

@media screen and (max-width: 375px) {
  .gingerbread-text {
    font-size: 2.4em;
    -webkit-text-stroke: 1px #8b4513;
    margin-bottom: 12px;
  }

  .year-text {
    font-size: 2em;
    -webkit-text-stroke: 1px #ff0000;
    margin-top: 5px;
  }

  .theme-text {
    font-size: 1.4em;
  }

  .content {
    padding-top: 20px;
  }

  .winter-background {
    padding: 10px;
  }

  .judges-container {
    gap: 20px;
  }
}

.gingerbread-text:hover {
  transform: scale(1.05);
}

.year-text {
  font-family: "Fredoka", sans-serif;
  font-size: 3.6em;
  background: linear-gradient(45deg, #ff0000, #ff4d4d);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
  letter-spacing: 4px;
  font-weight: 700;
  -webkit-text-stroke: 1.5px #ff0000;
  transform: scale(1.1);
  margin-top: 10px;
  transition: transform 0.3s ease;
}

.year-text:hover {
  transform: scale(1.15);
}

.theme-section {
  margin-top: 30px;
  margin-bottom: 20px;
}

.theme-text {
  font-family: "Fredoka", sans-serif;
  font-size: 1.8em;
  color: white;
  font-weight: 700;
  letter-spacing: 1px;
  transition: transform 0.3s ease;
  display: inline-block;
  position: relative;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
}

.theme-text::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: -5px;
  width: 100%;
  height: 3px;
  background: white;
  border-radius: 2px;
  box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
}

.theme-text:hover {
  transform: scale(1.05);
}

.snowflake {
  color: #fff;
  font-size: 1.2em;
  position: absolute;
  top: -20px;
  animation: fall linear;
  text-shadow: 0 0 5px rgba(255, 255, 255, 0.8);
  opacity: 0;
  will-change: transform;
}

@keyframes fall {
  0% {
    transform: translateY(0) translateX(-5px) rotate(0deg);
    opacity: 0;
  }
  5% {
    opacity: 0.85;
  }
  90% {
    opacity: 0.85;
  }
  100% {
    transform: translateY(110vh) translateX(5px) rotate(360deg);
    opacity: 0;
  }
}

.snowflakes .snowflake {
  animation-duration: var(--fall-duration);
  animation-name: fall;
}

.judges-container {
  display: flex;
  flex-direction: column;
  gap: 40px;
  align-items: center;
}
</style>
