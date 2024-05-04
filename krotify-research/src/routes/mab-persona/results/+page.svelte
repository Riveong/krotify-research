<script>
    import { onMount } from 'svelte';
    import Nav from '../../nav.svelte';
    
    let data;
  
    onMount(() => {
      const storedData = sessionStorage.getItem('apiResponse');
      if (storedData) {
        data = JSON.parse(storedData);
      }
    });
  
    // Helper function to count unique values
    function countUniqueValues(values) {
      return values.reduce((acc, value) => {
        acc[value] = (acc[value] || 0) + 1;
        return acc;
      }, {});
    }
      // Function to find the most frequent value
  function findMostFrequentValue(values) {
    const counts = countUniqueValues(values);
    return Object.entries(counts).reduce((a, b) => a[1] > b[1] ? a : b);
  }
  </script>
  
  <Nav />
  
  <div class="bod">
    <h1>Hasil</h1>
    {#if data}
      <div class="card">
        <h2>Hasil pencocokan 1</h2>
        <p>Karakteristik: {findMostFrequentValue(data.data[0].epsilon_greedy)[0]} (Appeared {findMostFrequentValue(data.data[0].epsilon_greedy)[1]} times)</p>
      </div>
      <div class="card">
        <h2>Hasil pecocokan 2</h2>
        <p>Karakteristik: {findMostFrequentValue(data.data[1].thompson_sampling)[0]} (Appeared {findMostFrequentValue(data.data[1].thompson_sampling)[1]} times)</p>
      </div>
      <h1>Analisa Hasil</h1>
      <div class="card">
        <h2>Most Frequent Matchmaking Value (Epsilon-Greedy Algorithm)</h2>
        <p>Most Frequent Value: {findMostFrequentValue(data.data[0].epsilon_greedy)[0]} (Appeared {findMostFrequentValue(data.data[0].epsilon_greedy)[1]} times)</p>
      </div>
      <div class="card">
        <h2>Most Frequent Matchmaking Value (Thompson Sampling)</h2>
        <p>Most Frequent Value: {findMostFrequentValue(data.data[1].thompson_sampling)[0]} (Appeared {findMostFrequentValue(data.data[1].thompson_sampling)[1]} times)</p>
      </div>
      <div class="card">
        <h2>Matchmaking Results (Epsilon-Greedy Algorithm)</h2>
        <p>Reward Value: {data.data[0].epsilon_reward}</p>
        <h3>Matchmaking Values Distribution:</h3>
        <ul>
          {#each Object.entries(countUniqueValues(data.data[0].epsilon_greedy)) as [value, count]}
            <li>{value} appeared {count} times</li>
          {/each}
        </ul>
      </div>
      <div class="card">
        <h2>Matchmaking Results (Thompson Sampling)</h2>
        <p>Reward Value: {data.data[1].thompson_reward}</p>
        <h3>Matchmaking Values Distribution:</h3>
        <ul>
          {#each Object.entries(countUniqueValues(data.data[1].thompson_sampling)) as [value, count]}
            <li>{value} appeared {count} times</li>
          {/each}
        </ul>
      </div>
    {:else}
      <p>No data available. Please complete the survey.</p>
    {/if}
  </div>
  
  <style>
    .bod {
      padding: 0 50px;
    }
    .card {
      background-color: white;
      border: 1px solid #ccc;
      padding: 20px;
      border-radius: 8px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
      margin-top: 20px;
    }
    h2, h3 {
      color: #333;
    }
    ul {
      margin-top: 10px;
      list-style-type: none;
      padding: 0;
    }
    li {
      padding: 5px 0;
    }
  </style>
  