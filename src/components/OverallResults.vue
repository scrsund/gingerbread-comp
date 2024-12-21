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
  max-width: 700px;
  margin-left: auto;
  margin-right: auto;
}

.overall-title {
  font-family: "Titan One", cursive;
  font-size: 2em;
  background: linear-gradient(45deg, #8b4513, #a0522d);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
  letter-spacing: 1px;
  margin-bottom: 30px;
  transition: transform 0.3s ease;
  -webkit-text-stroke: 1px #8b4513;
  opacity: 0.9;
}

.overall-title:hover {
  transform: scale(1.05);
}

@media screen and (max-width: 768px) {
  .overall-results {
    margin-top: 40px;
    padding: 20px;
    width: 90%;
  }

  .overall-title {
    font-size: 1.8em;
    margin-bottom: 20px;
  }

  .final-country-score {
    padding: 12px;
  }

  .country-name {
    font-size: 1.2em;
  }

  .total-points {
    font-size: 0.9em;
  }

  .place-badge {
    font-size: 0.9em;
    padding: 5px 10px;
  }
}

@media screen and (max-width: 480px) {
  .overall-results {
    margin-top: 30px;
    padding: 15px;
    width: 95%;
  }

  .overall-title {
    font-size: 1.6em;
    margin-bottom: 15px;
  }

  .final-country-score {
    padding: 10px;
  }

  .country-name {
    font-size: 1.1em;
  }

  .total-points {
    font-size: 0.85em;
    padding: 5px 10px;
  }

  .place-badge {
    font-size: 0.85em;
    padding: 4px 8px;
    margin-right: 10px;
  }

  .final-scores {
    gap: 10px;
  }
}

@media screen and (max-width: 375px) {
  .overall-results {
    margin-top: 20px;
    padding: 10px;
    width: 100%;
  }

  .overall-title {
    font-size: 1.4em;
    margin-bottom: 12px;
  }

  .final-country-score {
    padding: 8px;
  }

  .country-name {
    font-size: 1em;
  }

  .total-points {
    font-size: 0.8em;
    padding: 4px 8px;
    margin-left: 8px;
  }

  .place-badge {
    font-size: 0.8em;
    padding: 3px 6px;
    margin-right: 8px;
  }

  .final-scores {
    gap: 8px;
  }
}

.final-scores {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.final-country-score {
  display: flex;
  align-items: center;
  padding: 15px;
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
  padding: 6px 12px;
  border-radius: 15px;
  font-weight: 700;
  font-size: 1em;
  margin-right: 15px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.country-name {
  font-size: 1.4em;
  font-weight: 600;
  color: #2c3e50;
  flex-grow: 1;
}

.total-points {
  font-size: 1em;
  font-weight: 700;
  color: #2c3e50;
  padding: 6px 12px;
  background: rgba(255, 255, 255, 0.8);
  border-radius: 8px;
  margin-left: 15px;
}
</style>
