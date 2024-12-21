<template>
  <div class="overall-results">
    <h2 class="overall-title">Final Results</h2>
    <div class="final-scores">
      <div
        v-for="(score, index) in sortedTotalScores"
        :key="score.country"
        class="final-country-score"
        :class="getPlaceClass(index)"
      >
        <div class="place-badge">{{ getPlaceText(index) }}</div>
        <span class="country-name">{{ score.country }}</span>
        <span class="total-points">{{ score.points }} points</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "OverallResults",
  props: {
    judgesScores: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      pointValues: {
        "1st": 3,
        "2nd": 2,
        "3rd": 1,
      },
    };
  },
  computed: {
    sortedTotalScores() {
      const totals = {
        Brazil: 0,
        Switzerland: 0,
        Egypt: 0,
      };

      // Calculate total points from all judges
      Object.values(this.judgesScores).forEach((judgeScore) => {
        Object.values(judgeScore).forEach((category) => {
          Object.entries(category).forEach(([place, country]) => {
            totals[country] += this.pointValues[place];
          });
        });
      });

      return Object.entries(totals)
        .map(([country, points]) => ({
          country,
          points,
        }))
        .sort((a, b) => b.points - a.points);
    },
  },
  methods: {
    getPlaceClass(index) {
      return `place-${index + 1}`;
    },
    getPlaceText(index) {
      const places = ["1st", "2nd", "3rd"];
      return places[index];
    },
  },
};
</script>

<style scoped>
.overall-results {
  margin-top: 60px;
  padding: 30px;
  background: rgba(255, 255, 255, 0.95);
  border-radius: 20px;
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.15);
  max-width: 800px;
  margin-left: auto;
  margin-right: auto;
}

.overall-title {
  font-family: "Fredoka", sans-serif;
  font-size: 2.5em;
  color: #8b4513;
  margin-bottom: 30px;
  font-weight: 700;
  text-align: center;
  background: linear-gradient(45deg, #8b4513, #a0522d);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
  -webkit-text-stroke: 1px #8b4513;
}

.final-scores {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.final-country-score {
  display: flex;
  align-items: center;
  padding: 20px;
  border-radius: 12px;
  font-family: "Fredoka", sans-serif;
  position: relative;
  transition: transform 0.3s ease;
}

.final-country-score:hover {
  transform: scale(1.02);
}

.place-1 {
  background: linear-gradient(45deg, #ffd700, #ffc800);
  box-shadow: 0 4px 15px rgba(255, 215, 0, 0.3);
}

.place-2 {
  background: linear-gradient(45deg, #c0c0c0, #e0e0e0);
  box-shadow: 0 4px 15px rgba(192, 192, 192, 0.3);
}

.place-3 {
  background: linear-gradient(45deg, #cd7f32, #dfa878);
  box-shadow: 0 4px 15px rgba(205, 127, 50, 0.3);
}

.place-badge {
  background: white;
  padding: 8px 16px;
  border-radius: 20px;
  font-weight: 700;
  font-size: 1.2em;
  margin-right: 20px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.country-name {
  font-size: 1.8em;
  font-weight: 600;
  color: #2c3e50;
  flex-grow: 1;
}

.total-points {
  font-size: 1.2em;
  font-weight: 700;
  color: #2c3e50;
  padding: 6px 12px;
  background: rgba(255, 255, 255, 0.8);
  border-radius: 8px;
  margin-left: 20px;
}
</style>
