<template>
    <div id="dashboard">
      <!-- Header Section -->
      <header class="header">
        <h1>Player Dashboard</h1>
        <nav class="navbar">
          <select v-model="teamFilter" @change="filterByTeam">
            <option value="ALL">All Teams</option>
            <option value="IND">India</option>
            <option value="PAK">Pakistan</option>
            <option value="AUS">Australia</option>
            <option value="ENG">England</option>
          </select>
          <div class="search-container">
            <input v-model="query" placeholder="Search by name or team..." />
            <button @click="searchPlayer">Search</button>
          </div>
        </nav>
      </header>
  
      <!-- Player List Section -->
      <main class="main-content">
        <div v-for="(group, role) in categorizedPlayers" :key="role" class="role-group">
          <h2>{{ mapRoleToText(role) }}</h2>
          <div class="players-wrapper">
            <div v-for="player in group" :key="player.name" class="player-card">
              <img :src="player.image" :alt="player.name" class="player-image" />
              <div class="player-info">
                <h3>{{ player.name }}</h3>
                <p>{{ player.team_name }}</p>
                <ul class="stats">
                  <li>Matches: {{ player.matches }}</li>
                  <li>Runs: {{ player.runs }}</li>
                  <li>50s/100s: {{ player['50s'] }}/{{ player['100s'] }}</li>
                  <li>Highest: {{ player.highest_score }}</li>
                  <li>Bowling Figures: {{ player.best_bowling_figures || 'N/A' }}</li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </main>
    </div>
  </template>
  
  <script>
  import playersData from '../assets/players.json';
  
  export default {
    name: 'PlayerDashboard',
    data() {
      return {
        allPlayers: [], // Raw player data
        filteredPlayers: [], // Players after filtering
        teamFilter: 'ALL', // Team filter selection
        query: '', // Search query
      };
    },
    computed: {
      categorizedPlayers() {
        let categories = { 2: [], 3: [], 4: [] };
        this.filteredPlayers.forEach(player => {
          if (categories[player.role]) {
            categories[player.role].push(player);
          }
        });
        return categories;
      },
    },
    methods: {
      loadPlayers() {
        this.allPlayers = playersData.originalPlayers;
        this.filteredPlayers = this.allPlayers; // Initialize with full data
      },
      filterByTeam() {
        if (this.teamFilter === 'ALL') {
          this.filteredPlayers = this.allPlayers;
        } else {
          this.filteredPlayers = this.allPlayers.filter(player => player.team_name === this.teamFilter);
        }
      },
      searchPlayer() {
        const query = this.query.toLowerCase();
        this.filteredPlayers = this.allPlayers.filter(player =>
          player.name.toLowerCase().includes(query) || player.team_name.toLowerCase().includes(query)
        );
      },
      mapRoleToText(role) {
        const roleMap = { 2: 'Batsmen', 3: 'All-rounders', 4: 'Bowlers' };
        return roleMap[role] || 'Unknown Role';
      },
    },
    created() {
      this.loadPlayers();
    },
  };
  </script>
  
  <style scoped>
  #dashboard {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  
  .header {
    background-color: #f5f5f5;
    padding: 20px;
    text-align: center;
  }
  
  .navbar {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: 20px;
  }
  
  .navbar select {
    margin-right: 20px;
    padding: 5px;
  }
  
  .search-container {
    display: flex;
    align-items: center;
  }
  
  .search-container input {
    padding: 5px;
    margin-right: 10px;
  }
  
  .search-container button {
    padding: 5px 10px;
    background-color: #28a745;
    color: white;
    border: none;
    cursor: pointer;
  }
  
  .main-content {
    padding: 20px;
  }
  
  .role-group {
    margin-bottom: 40px;
  }
  
  .players-wrapper {
    display: flex;
    flex-wrap: wrap;
    gap: 15px;
  }
  
  .player-card {
    background-color: #fff;
    border: 1px solid #ddd;
    border-radius: 10px;
    width: 250px;
    padding: 15px;
    box-shadow: 2px 4px 10px rgba(0, 0, 0, 0.1);
    transition: 0.3s;
  }
  
  .player-card:hover {
    transform: scale(1.05);
  }
  
  .player-image {
    width: 100%;
    height: 150px;
    object-fit: cover;
    margin-bottom: 10px;
    border-radius: 8px;
  }
  
  .player-info {
    text-align: center;
  }
  
  .player-info h3 {
    font-size: 18px;
    margin-bottom: 10px;
  }
  
  .stats {
    list-style: none;
    padding: 0;
  }
  
  .stats li {
    font-size: 14px;
    margin: 5px 0;
  }
  </style>
  