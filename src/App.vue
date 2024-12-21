<template>
  <div class="winter-background">
    <div class="snowflakes" aria-hidden="true">
      <div 
        v-for="snowflake in snowflakes" 
        :key="snowflake.id" 
        class="snowflake"
        :style="snowflake.style"
        @animationend="removeSnowflake(snowflake.id)"
      >{{ snowflake.char }}</div>
    </div>
    <div class="content">
      <div class="gingerbread-text">Gingerbread House Competition</div>
      <div class="year-text">2024</div>
      
      <div class="scores-section">
        <h2 class="judge-name">Keely</h2>
        <table class="scores-table">
          <thead>
            <tr>
              <th>Category</th>
              <th>1st Place</th>
              <th>2nd Place</th>
              <th>3rd Place</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>Technical</td>
              <td>Brazil</td>
              <td>Switzerland</td>
              <td>Egypt</td>
            </tr>
            <tr>
              <td>Creativity</td>
              <td>Switzerland</td>
              <td>Egypt</td>
              <td>Brazil</td>
            </tr>
            <tr>
              <td>Appearance</td>
              <td>Switzerland</td>
              <td>Egypt</td>
              <td>Brazil</td>
            </tr>
            <tr>
              <td>Best Overall</td>
              <td>Switzerland</td>
              <td>Brazil</td>
              <td>Egypt</td>
            </tr>
          </tbody>
        </table>
        <div class="total-scores">
          <h3>Total Scores</h3>
          <div class="points-info">Points: 1st Place (3 pts), 2nd Place (2 pts), 3rd Place (1 pt)</div>
          <div v-for="score in totalScores" :key="score.country" class="country-total">
            <span class="country-name">{{ score.country }}</span>
            <span class="country-points">{{ score.points }} points</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      snowflakes: [],
      nextId: 0,
      columns: 18,
      columnStatus: {},
      baseSpeed: 20,
      generationInterval: null,
      isGenerating: false,
      scores: {
        Technical: { '1st': 'Brazil', '2nd': 'Switzerland', '3rd': 'Egypt' },
        Creativity: { '1st': 'Switzerland', '2nd': 'Egypt', '3rd': 'Brazil' },
        Appearance: { '1st': 'Switzerland', '2nd': 'Egypt', '3rd': 'Brazil' },
        'Best Overall': { '1st': 'Switzerland', '2nd': 'Brazil', '3rd': 'Egypt' }
      },
      pointValues: {
        '1st': 3,
        '2nd': 2,
        '3rd': 1
      }
    }
  },
  computed: {
    totalScores() {
      const totals = {
        Brazil: 0,
        Switzerland: 0,
        Egypt: 0
      };

      Object.entries(this.scores).forEach(([, places]) => {
        Object.entries(places).forEach(([place, country]) => {
          totals[country] += this.pointValues[place];
        });
      });

      return Object.entries(totals)
        .map(([country, points]) => ({
          country,
          points
        }))
        .sort((a, b) => b.points - a.points);
    }
  },
  methods: {
    getAvailableColumn() {
      const now = Date.now();
      const availableColumns = Array.from({ length: this.columns })
        .map((_, i) => i)
        .filter(col => {
          const lastSpawn = this.columnStatus[col] || 0;
          return now - lastSpawn > 2500;
        });
      
      if (availableColumns.length === 0) return null;
      return availableColumns[Math.floor(Math.random() * availableColumns.length)];
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
        '--fall-duration': `${duration}s`,
        'left': `${position}%`,
      };
      
      this.columnStatus[column] = Date.now();
      
      return {
        id,
        style,
        char: id % 2 === 0 ? '❅' : '❆',
        column
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
      const index = this.snowflakes.findIndex(s => s.id === id);
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
    }
  },
  mounted() {
    this.startSnowfall();
  },
  beforeUnmount() {
    this.stopSnowfall();
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

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
  font-family: 'Fredoka', sans-serif;
  font-size: 3.6em;
  background: linear-gradient(45deg, #8B4513, #A0522D);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
  letter-spacing: 1px;
  margin-bottom: 20px;
  transition: transform 0.3s ease;
  font-weight: 700;
  -webkit-text-stroke: 2px #8B4513;
}

.gingerbread-text:hover {
  transform: scale(1.05);
}

.year-text {
  font-family: 'Fredoka', sans-serif;
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

@import url('https://fonts.googleapis.com/css2?family=Fredoka:wght@300;400;500;600;700&display=swap');

.scores-section {
  margin-top: 40px;
  padding: 20px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 15px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
  max-width: 600px;
  margin-left: auto;
  margin-right: auto;
}

.judge-name {
  font-family: 'Fredoka', sans-serif;
  font-size: 2em;
  color: #8B4513;
  margin-bottom: 20px;
  font-weight: 600;
}

.scores-table {
  width: 100%;
  border-collapse: separate;
  border-spacing: 0;
  margin-bottom: 20px;
}

.scores-table th,
.scores-table td {
  padding: 10px;
  text-align: center;
  font-family: 'Fredoka', sans-serif;
  border: 2px solid #e1e1e1;
}

.scores-table th {
  background: linear-gradient(45deg, #c68b59, #dba17c);
  color: white;
  font-weight: 600;
  font-size: 1.1em;
}

.scores-table td {
  font-size: 1em;
  color: #2c3e50;
}

.scores-table tr:nth-child(even) {
  background-color: rgba(255, 255, 255, 0.7);
}

.scores-table tr:nth-child(odd) {
  background-color: rgba(255, 255, 255, 0.9);
}

.scores-table tr:hover {
  background-color: rgba(198, 139, 89, 0.1);
}

.scores-table td:first-child {
  font-weight: 800;
  font-size: 1.2em;
  color: #2c3e50;
  background-color: rgba(255, 255, 255, 0.95);
}

.scores-table tr:hover td:first-child {
  background-color: rgba(255, 255, 255, 1);
}

.scores-table th:first-child {
  border: 1px solid #e1e1e1;
}

.scores-table td {
  font-size: 1em;
  color: #2c3e50;
  border: 1px solid #e1e1e1;
}

.total-scores {
  margin-top: 30px;
  padding: 15px;
  background: rgba(255, 255, 255, 0.95);
  border-radius: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.total-scores h3 {
  color: #2c3e50;
  margin-bottom: 15px;
  font-family: 'Fredoka', sans-serif;
  font-size: 1.5em;
}

.points-info {
  font-size: 0.9em;
  color: #666;
  margin-bottom: 15px;
  font-style: italic;
}

.country-total {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 15px;
  margin: 5px 0;
  background: rgba(198, 139, 89, 0.1);
  border-radius: 8px;
  font-family: 'Fredoka', sans-serif;
}

.country-name {
  font-weight: 600;
  font-size: 1.1em;
  color: #2c3e50;
}

.country-points {
  font-weight: 700;
  font-size: 1.2em;
  color: #c68b59;
}
</style>
