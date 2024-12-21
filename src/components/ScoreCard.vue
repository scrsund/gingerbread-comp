<template>
  <div class="scores-section">
    <h2 class="judge-name">{{ displayName }}</h2>
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
        <tr v-for="(places, category) in scores" :key="category">
          <td>{{ category }}</td>
          <td :class="{ 'switzerland-cell': places['1st'] === 'Switzerland' }">{{ places["1st"] }}</td>
          <td :class="{ 'switzerland-cell': places['2nd'] === 'Switzerland' }">{{ places["2nd"] }}</td>
          <td :class="{ 'switzerland-cell': places['3rd'] === 'Switzerland' }">{{ places["3rd"] }}</td>
        </tr>
      </tbody>
    </table>
    <div class="total-scores">
      <h3>Total Scores</h3>
      <div
        v-for="score in totalScores"
        :key="score.country"
        class="country-total"
      >
        <span class="country-name" :class="{ 'switzerland-text': score.country === 'Switzerland' }">{{ score.country }}</span>
        <span class="country-points">{{ score.points }} points</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ScoreCard",
  props: {
    judgeName: {
      type: String,
      required: true,
    },
    scores: {
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
    displayName() {
      return this.judgeName.charAt(0).toUpperCase() + this.judgeName.slice(1);
    },
    totalScores() {
      const totals = {
        Brazil: 0,
        Switzerland: 0,
        Egypt: 0,
      };

      Object.entries(this.scores).forEach(([, places]) => {
        Object.entries(places).forEach(([place, country]) => {
          totals[country] += this.pointValues[place];
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
};
</script>

<style scoped>
.scores-section {
  margin-top: 40px;
  padding: 20px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 15px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
  max-width: 500px;
  margin-left: auto;
  margin-right: auto;
}

@media screen and (max-width: 768px) {
  .scores-section {
    padding: 15px;
    max-width: 450px;
  }

  .scores-table {
    font-size: 0.9em;
  }

  .scores-table th,
  .scores-table td {
    padding: 8px 4px;
  }
}

@media screen and (max-width: 480px) {
  .scores-section {
    padding: 12px;
    max-width: none;
    width: 95%;
  }

  .judge-name {
    font-size: 1.5em;
  }

  .scores-table {
    font-size: 0.75em;
  }

  .scores-table th {
    font-size: 0.8em;
    padding: 6px 2px;
  }

  .scores-table td {
    font-size: 0.8em;
    padding: 6px 2px;
  }

  .scores-table td:first-child {
    font-size: 0.8em;
  }

  .total-scores {
    padding: 12px;
  }

  .total-scores h3 {
    font-size: 1.3em;
  }

  .points-info {
    font-size: 0.8em;
  }

  .country-name {
    font-size: 0.9em;
  }

  .country-points {
    font-size: 1em;
  }
}

@media screen and (max-width: 480px) {
  .scores-section {
    margin-top: 20px;
    padding: 8px;
    width: 100%;
  }

  .judge-name {
    font-size: 1.3em;
    margin-bottom: 10px;
  }

  .scores-table {
    font-size: 0.65em;
    margin-bottom: 15px;
  }

  .scores-table th {
    padding: 4px 1px;
    font-size: 0.7em;
  }

  .scores-table td {
    padding: 4px 1px;
    font-size: 0.7em;
  }

  .scores-table td:first-child {
    font-size: 0.7em;
    padding-left: 1px;
    padding-right: 1px;
  }

  .total-scores {
    margin-top: 15px;
    padding: 8px;
  }

  .total-scores h3 {
    font-size: 1.1em;
    margin-bottom: 8px;
  }

  .points-info {
    font-size: 0.7em;
    margin-bottom: 8px;
  }

  .country-total {
    padding: 6px 8px;
    margin: 4px 0;
  }

  .country-name {
    font-size: 0.8em;
  }

  .country-points {
    font-size: 0.9em;
  }
}

.judge-name {
  font-family: "Fredoka", sans-serif;
  font-size: 2em;
  color: #8b4513;
  margin-bottom: 20px;
  font-weight: 600;
}

.scores-table {
  width: 100%;
  border-collapse: separate;
  border-spacing: 0;
  margin-bottom: 20px;
  table-layout: fixed;
}

.scores-table th,
.scores-table td {
  padding: 10px 5px;
  text-align: center;
  font-family: "Fredoka", sans-serif;
  border: 1px solid #e1e1e1;
  overflow: hidden;
  text-overflow: ellipsis;
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

.scores-table td:first-child {
  font-weight: 800;
  font-size: 1.1em;
  width: 28%;
}

.scores-table th:not(:first-child),
.scores-table td:not(:first-child) {
  width: 24%;
}

.scores-table tr {
  background-color: rgba(255, 255, 255, 0.9);
  transition: background-color 0.2s ease;
}

.scores-table tr:nth-child(even) {
  background-color: rgba(255, 255, 255, 0.7);
}

.scores-table tr:nth-child(odd) {
  background-color: rgba(255, 255, 255, 0.9);
}

.scores-table th:first-child {
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
  font-family: "Fredoka", sans-serif;
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
  font-family: "Fredoka", sans-serif;
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

.scores-table tr:hover {
  background-color: rgba(198, 139, 89, 0.15);
  cursor: pointer;
}
</style>
