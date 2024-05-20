<script>
  import { onMount } from "svelte";
  import Nav from "../../nav.svelte";

  let data;
  let dataEp = null;
  let dataEpSim = null;
  let dataTp = null;
  let dataTpSim = null;
  let dataLn = null;
  let dataLnSim = null;
  let userData;
  let nama;
  let umur;
  let gender;
  let genderShow;
  let answers;

  let epsilonGreedyRating = null;
  let thompsonSamplingRating = null;
  let linearRating = null;

  async function endAll(){
    const fdataEp = await dataEpSim;
    const fdataTs = await dataTpSim;
    const fdataLn = await dataLnSim;
    const fdataHS = await [data.data[0].epsilon_reward,data.data[1].thompson_reward,data.data[2].lin_greedy_reward];

      // Check if at least one rating has been given
  if (epsilonGreedyRating === null && thompsonSamplingRating === null && linearRating == null) {
    // Alert the user if no ratings have been provided
    alert("Mohon isi beri rating setidaknya salah satu saja");
    return; // Stop the function from proceeding further
  }
    
    const end = {
      "nama":nama,
      "umur":umur,
      "gender":gender,
      "epsilon_greedy_id":fdataEp[2],
      "thompson_sampling_id":fdataTs[2],
      "linear_id":fdataLn[2],
      "user_input":answers,
      "rating":[epsilonGreedyRating,thompsonSamplingRating,linearRating],
      "reward":fdataHS
    }
    try {
      const response = await fetch(`${host}submit-data/`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(end)
      });

      if (!response.ok) {
        throw new Error('Network response was not ok');
      }

      // Navigate to google.com on success
      window.location.href = './results/done';
    } catch (error) {
      // Alert the user if the request fails
      alert('Failed to submit data: ' + error.message);
    }
  }
  

  
  function setRating(algorithm, rating) {
    if (algorithm === 'epsilon') {
      epsilonGreedyRating = rating;
    } else if (algorithm === 'thompson') {
      thompsonSamplingRating = rating;
    } else if (algorithm === 'linear') {
      linearRating = rating;
  }
}
  const questions = [
    "suka mengekspresikan diri.",
    "suka mencoba memimpin orang.",
    "suka mengambil tanggung jawab",
    "tahu bagaimana cara meyakinkan orang lain",
    "suka bertindak lebih dahulu daripada orang lain",
    "bisa mengambil alih kontrol",
    "biasa menunggu orang lain untuk memimpin",
    "suka membiarkan orang lain mengambil keputusan",
    "tidak terlalu termotivasi untuk sukses",
    "tidak bisa mendapatkan ide baru",
    "nyaman berada diantara banyak orang",
    "tidak risih saat menjadi pusat perhatian",
    "hebat saat pidato dengan metode improvisasi",
    "mudah menyampaikan ekspresinya",
    "memiliki bakat natural dalam mempengaruhi orang lain",
    "risih saat menjadi pusat perhatian",
    "tidak mempunyai bakat untuk mempengaruhi orang lain",
    "tidak senang berada ditengah orang lain",
    "tidak suka memperhatikan diri sendiri",
    "singkat, jelas, padat",
    "lebih suka rutinitas yang bervariasi",
    "suka mengunjungi tempat baru",
    "mempunyai ketertarikan dibanyak hal",
    "suka memulai dengan hal baru",
    "suka bermain di zona nyaman mereka",
    "tidak suka hal baru",
    "tidak suka ide dari 'hal baru'",
    "adalah seorang yang suka dengan 'kebiasaan'",
    "tidak suka mencoba / memakan makanan yang baru",
    "suka hal – hal yang konvensional",
    "kompetitif",
    "selalu mencoba untuk menjadi lebih berhasil daripada orang lain",
    "cepat suka mengkoreksi orang lain",
    "suka memaksa kehendak terhadap orang lain",
    "sering meminta penjelasan terhadap orang lain",
    "suka mengontrol percakapan",
    "takut memberikan kritik terhadap orang lain",
    "suka menantang / berdebat karena pandangan yang berbeda yang dipunyai orang lain",
    "suka memerintah orang lain",
    "suka membuat orang lain dibawah tekanan",
  ];

  onMount(() => {
    const storedData = sessionStorage.getItem("apiResponse");
    const userDatas = sessionStorage.getItem("dataStoreMAB");

    if (userDatas){
      userData = JSON.parse(userDatas);
      nama = userData.nama;
      umur = userData.umur;
      gender = userData.gender;
      answers = userData.answer;
      if (gender === 1){
        genderShow = "pria"
      } else{ genderShow = "wanita"}
    }
    if (storedData) {
      data = JSON.parse(storedData);
      dataEp = find_data(findMostFrequentValue(data.data[0].epsilon_greedy)[0]);
      dataEpSim = find_sim(
        findMostFrequentValue(data.data[0].epsilon_greedy)[0],
      );
      dataTp = find_data(
        findMostFrequentValue(data.data[1].thompson_sampling)[0],
      );
      dataTpSim = find_sim(
        findMostFrequentValue(data.data[1].thompson_sampling)[0],
      );
      dataLn = find_data(findMostFrequentValue(data.data[2].lin_greedy)[0]);
      dataLnSim = find_sim(
        findMostFrequentValue(data.data[2].lin_greedy)[0],
      );
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
    return Object.entries(counts).reduce((a, b) => (a[1] > b[1] ? a : b));
  }

  async function find_sim(id) {
    try {
      const response = await fetch(
        `${host}search_similarity/${id}`,
      );
      const responseData = await response.json();
      return responseData.data;
    } catch (err) {
      console.error("Error:", err);
    }
  }

  async function find_data(id) {
    try {
      const response = await fetch(`${host}search_data/${id}`);
      const responseData = await response.json();
      return responseData.data;
    } catch (err) {
      console.error("Error:", err);
    }
  }

  function filterQuestions(scores, questions) {
    const filtered = scores.slice(0, 40);
    return questions.filter((q, index) => filtered[index] > 4);
  }
</script>

<Nav />

<div class="bod">
  <h1>Hasil Survey</h1>
  <p>Terimakasih sudah mengisi survey! 1 tahap lagi untuk menyelesaikan survey ini secara keseluruhan... mohon cermati data diri anda dan beri rating (like dan dislike) pada hasil matchmaking.</p>
  <h2>Data diri</h2>
  {#await userData}
    <p>Loading ...</p>
  {:then userData} 
  <p>Nama: {nama}</p>
  <p>Umur: {umur}</p>
  <p>Gender: {genderShow}</p>
  {/await}

<h1>Hasil matchmaking</h1>
  {#if data}
    <div class="card-ep">
      <h2 style="color: white;">Guru Epsilon Greedy</h2>
      <p style="color: white;">
        {findMostFrequentValue(data.data[0].epsilon_greedy)[0]} (Appeared {findMostFrequentValue(
          data.data[0].epsilon_greedy,
        )[1]} times)
      </p>
      {#await dataEp}
        <p>Loading...</p>
      {:then dataEp}
        <b style="color: white;">Karakter yang kuat:</b>
        {#each filterQuestions(dataEp, questions) as question}
        <span class="question-card">
          + {question}
        </span>
      {/each}
      {/await}
      {#await dataEpSim}
        <p>Loading...</p>
      {:then dataEpSim}
        <p style="color: white;"><b>Persentase Similaritas: </b>{dataEpSim[3]}</p>
      {/await}
      <button
      class:default={epsilonGreedyRating === null}
      class:passive={epsilonGreedyRating === 0}
      class:liked={epsilonGreedyRating === 1}
        on:click={() => setRating('epsilon', 1)}>
        👍
      </button>
      <button
      class:default={epsilonGreedyRating === null}
      class:passive={epsilonGreedyRating === 1}
      class:disliked={epsilonGreedyRating === 0}
        on:click={() => setRating('epsilon', 0)}>
        👎
      </button>

    </div>
    
    <div class="card-ts">
      <h2 style="color: white;">Guru Thompson Sampling</h2>
      <p style="color: white;">
        {findMostFrequentValue(data.data[1].thompson_sampling)[0]} (Appeared {findMostFrequentValue(
          data.data[1].thompson_sampling,
        )[1]} times)
      </p>
      {#await dataTp}
        <p>Loading...</p>
      {:then dataTp}
      <b style="color: white;">Karakter yang kuat:</b>
        {#each filterQuestions(dataTp, questions) as question}
          <span class="question-card">
              + {question}
          </span>
        {/each}
      {/await}
      {#await dataTpSim}
        <p>Loading...</p>
      {:then dataTpSim}
        <p style="color: white;"><b>Persentase Similaritas: </b>{dataTpSim[3]}</p>
      {/await}
      <button
        class:default={thompsonSamplingRating === null}
        class:liked={thompsonSamplingRating === 1}
        class:passive={thompsonSamplingRating === 0}
        on:click={() => setRating('thompson', 1)}>
        👍
      </button>
      <button
        class:default={thompsonSamplingRating === null}
        class:passive={thompsonSamplingRating === 1}
        class:disliked={thompsonSamplingRating === 0}
        on:click={() => setRating('thompson', 0)}>
        👎
      </button>
    </div>


    <div class="card-lg">
      <h2 style="color: white;">Guru Linear Greedy</h2>
      <p style="color: white;">
        {findMostFrequentValue(data.data[2].lin_greedy)[0]} (Appeared {findMostFrequentValue(
          data.data[2].lin_greedy,
        )[1]} times)
      </p>
      {#await dataLn}
        <p>Loading...</p>
      {:then dataLn}
      <b style="color: white;">Karakter yang kuat:</b>

        {#each filterQuestions(dataLn, questions) as question}
          <span class="question-card">
              + {question}
          </span>
        {/each}
      {/await}
      {#await dataLnSim}
        <p>Loading...</p>
      {:then dataLnSim}
        <p style="color: white;"><b>Persentase Similaritas: </b>{dataLnSim[3]}</p>
      {/await}
      <button
      class:default={linearRating === null}
      class:liked={linearRating === 1}
      class:passive={linearRating === 0}
      on:click={() => setRating('linear', 1)}>
      👍
    </button>
    <button
      class:default={linearRating === null}
      class:passive={linearRating === 1}
      class:disliked={linearRating === 0}
      on:click={() => setRating('linear', 0)}>
      👎
    </button>
      
      </div>




    
<center>    
  <button class="end" on:click={endAll}>Selesai</button>
</center>


<hr class="seper">
    <h1>Analisa Hasil</h1>
    <div class="card">
      <h2>Most Frequent Matchmaking Value (Epsilon-Greedy Algorithm)</h2>
      <p>
        Most Frequent Value: {findMostFrequentValue(
          data.data[0].epsilon_greedy,
        )[0]} (Appeared {findMostFrequentValue(data.data[0].epsilon_greedy)[1]} times)
      </p>
    </div>
    <div class="card">
      <h2>Most Frequent Matchmaking Value (Thompson Sampling)</h2>
      <p>
        Most Frequent Value: {findMostFrequentValue(
          data.data[1].thompson_sampling,
        )[0]} (Appeared {findMostFrequentValue(
          data.data[1].thompson_sampling,
        )[1]} times)
      </p>
    </div>
    <div class="card">
      <h2>Most Frequent Matchmaking Value (Linear Greedy)</h2>
      <p>
        Most Frequent Value: {findMostFrequentValue(
          data.data[2].lin_greedy,
        )[0]} (Appeared {findMostFrequentValue(
          data.data[2].lin_greedy,
        )[1]} times)
      </p>
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
    <div class="card">
      <h2>Matchmaking Results (Linear Greedy)</h2>
      <p>Reward Value: {data.data[2].lin_greedy_reward}</p>
      <h3>Matchmaking Values Distribution:</h3>
      <ul>
        {#each Object.entries(countUniqueValues(data.data[2].lin_greedy)) as [value, count]}
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
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    margin-top: 20px;
  }
  .card-ep {
    background-color: rgb(44, 140, 229);
    border: 1px solid #ccc;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    margin-top: 20px;
  }
  .card-ts {
    background-color: rgb(255, 140, 0);
    border: 1px solid #ccc;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    margin-top: 20px;
  }
  .card-lg {
    background-color: rgb(0, 219, 113);
    border: 1px solid #ccc;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    margin-top: 20px;
  }
  h2,
  h3 {
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
  .question-card {
    display: flex; /* Enables Flexbox layout */ /* Aligns children horizontally */ /* Aligns children to the start of the flex container */ /* Vertically centers items in the container */
    gap: 5px; /* Adds space between children */
    color: #ffffff;
    margin: 5px;
    padding: 0px 10px;
    border-radius: 100px;
  }
  .liked {
    background-color: greenyellow;
    border: none;
    margin: 10px;
    border-radius: 100%;
    padding: 10px 10px;
    font-size: 20px;
  }
  .passive {
    background-color: transparent;
    border: 2px solid white;
    margin: 10px;
    border-radius: 100%;
    padding: 10px 10px;
    font-size: 20px;
  }
  .default {
    background-color: transparent;
    border: 2px solid white;
    margin: 10px;
    border-radius: 100%;
    padding: 10px 10px;
    font-size: 20px;
  }
  .disliked {
    background-color: red;
    border: none;
    margin: 10px;
    border-radius: 100%;
    padding: 10px 10px;
    font-size: 20px;
  }
  .end {
    background-color: black;
    color: white;
    border: none;
    margin: 10px;
    border-radius: 10px;
    padding: 10px 40px;
    font-size: 20px;
  }
  .seper{
    margin-top: 200px;
  }
</style>
