<script>
    import Nav from "../nav.svelte";

    let selectedNumbers = Array(40).fill(0);
    let name;
    let umur;
    let gender;
    
    let questions = [
    "AS1 Anda adalah seorang yang suka mengekspresikan diri.",
    "AS2 Anda adalah seorang yang suka mencoba memimpin orang.",
    "AS3 Anda adalah orang yang suka mengambil tanggung jawab",
    "AS4 Anda adalah seorang yang tahu bagaimana cara meyakinkan orang lain",
    "AS5 Anda adalah seorang yang suka bertindak lebih dahulu daripada orang lain",
    "AS6 Anda adalah seseorang yang bisa mengambil alih kontrol",
    "AS7 Anda adalah seorang yang biasa menunggu orang lain untuk memimpin",
    "AS8 Anda adalah seorang yang suka membiarkan orang lain mengambil keputusan",
    "AS9 Anda adalah seorang yang tidak terlalu termotivasi untuk sukses",
    "AS10 Anda adalah seorang yang tidak bisa mendapatkan ide baru",
    "SC1 Anda adalah seorang yang nyaman berada diantara banyak orang",
    "SC2 Anda tidak risih saat menjadi pusat perhatian",
    "SC3 Anda adalah seorang yang hebat saat pidato dengan metode improvisasi",
    "SC4 Anda adalah seorang yang dengan mudah menyampaikan ekspresinya",
    "SC5 Anda adalah seorang yang memiliki bakat natural dalam mempengaruhi orang lain",
    "SC6 Anda adalah orang yang risih saat menjadi pusat perhatian",
    "SC7 Anda adalah orang yang tidak mempunyai bakat untuk mempengaruhi orang lain",
    "SC8 Anda sering kali merasa tidak senang berada ditengah orang lain",
    "SC9 Anda tidak suka memperhatikan diri sendiri",
    "SC10 Anda merupakan orang yang singkat, jelas, padat",
    "AD1 Anda lebih suka rutinitas yang bervariasi",
    "AD2 Anda suka mengunjungi tempat baru",
    "AD3 Anda mempunyai ketertarikan dibanyak hal",
    "AD4 Anda suka memulai dengan hal baru",
    "AD5 Anda suka bermain di zona nyaman mereka",
    "AD6 Anda tidak suka hal baru",
    "AD7 Anda tidak suka ide dari 'hal baru'",
    "AD8 Anda adalah seorang yang suka dengan 'kebiasaan'",
    "AD9 Anda tidak suka mencoba / memakan makanan yang baru",
    "AD10 Anda suka dan teriakat dengan hal – hal yang konvensional",
    "DO1 Anda mencoba membalap hal yang telah dicapai oleh orang lain",
    "DO2 Anda selalu mencoba untuk menjadi lebih berhasil daripada orang lain",
    "DO3 Anda adalah seorang yang dengan cepat suka mengkoreksi orang lain",
    "DO4 Anda adalah seorang yang suka memaksa kehendak terhadap orang lain",
    "DO5 Anda adalah seorang yang sering meminta penjelasan terhadap orang lain",
    "DO6 Anda adalah seorang yang suka mengontrol percakapan",
    "DO7 Anda adalah seorang yang takut memberikan kritik terhadap orang lain",
    "DO8 Anda adalah seorang yang suka menantang / berdebat karena pandangan yang berbeda yang dipunyai orang lain",
    "DO9 Anda adalah seorang yang suka memerintah orang lain",
    "DO10 Anda adalah seorang yang suka membuat orang lain dibawah tekanan"
];


    async function submit(){
        name = document.getElementById("name").value;
        umur = document.getElementById("umur").value;
        gender = document.getElementById("gender").value;
        umur = parseInt(umur);
        gender = parseInt(gender);
        console.log(name);
        console.log(umur);
        console.log(gender);
        console.log(selectedNumbers);

        if (selectedNumbers.some(number => number === null || number === 0)) {
        alert("Mohon isi semua questioneer survey sebelum melanjutkan");
        return; // Stop the function execution here
    }

        const survey = {
            "nama":name,
            "umur":umur,
            "gender":gender,
            "answer":selectedNumbers
        }

        const data = {
            "answers":selectedNumbers,
            "age":umur,
            "gender":gender
        };


        try {
        const response = await fetch('http://127.0.0.1:8000/run_matchmaking', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(data)
        });
        const responseData = await response.json();
        sessionStorage.setItem('apiResponse', JSON.stringify(responseData)); // Save to session storage
        sessionStorage.setItem('dataStoreMAB', JSON.stringify(survey)); // Save to session storage

        window.location.href = './mab-persona/results'; // Redirect to the results page using window.location
    } catch (error) {
        console.error('Error:', error);
    }
}
    function selectNumber(number, index) {
        selectedNumbers[index] = number;
        selectedNumbers = selectedNumbers.slice();
    }

    function getColorForNumber(number) {
        switch(number) {
            case 1: return '#ff0008';
            case 2: return '#ff4249';
            case 3: return '#ffcfcf';
            case 4: return '#9cd2ff';
            case 5: return '#2ea1ff'; 
            default: return 'white';
        }
    }
</script>

<style>
    .button-container {
        display: flex; /* Set the container to use flexbox */
        justify-content: center; /* Center items horizontally */
        padding: 20px 20px;
    }
    .button-group {
        display: flex; /* Makes the container flex to align children vertically */
        flex-direction: column; /* Stack children vertically */
        align-items: center; /* Center-align the items */
        margin: 10px; /* Space between button groups */
    }
    button {
        margin-bottom: 5px; /* Space between button and caption */
        padding: 2vw 5vw;
        font-size: 20px;
        font-weight: bold;
        transition: background-color 0.3s; /* Smooth transition for background color */
        border-radius: 20px;
        border: 2px solid black;

    }
    .number-display {
        text-align: center;
        font-size: 14px; /* Caption text size */
    }
    .inside {
        padding: 0px 20px;
    }
    img {
    max-width: 100%;
    height: auto;
    }
    input{
        width: 80%;
        padding: 12px 20px;
    }
    .submit{
        padding: 10px 20px;
        font-size: 15px;
        border: 2px solid black;

    }
    .gender{
        padding: 20px 20px;
        font-size: 15px;
    }


</style>

<Nav />
<center>
    <h1>Matchmaking System Between <br>Student and Teacher
        with Multi Armed Bandits</h1>
    <img src="MAB.png" alt="" width="auto"/>
    <h2>Guidelines 📝</h2>
    <p style="padding: 0 6vw; font-size:18px; line-height: 2;">Selamat datang pada survey research matchmaking murid dan guru berdasarkan personalitas dengan memanfaatkan multi armed bandit 🙌. Disini anda akan diminta untuk mengisi survey kepribadian sejujur jujurnya. Prosedur yang akan dilalui adalah: <br> <b>Mengisi survey personalitas -> Program akan mendapatkan pasangan guru untuk anda -> memberikan feedback terhadap program.</b></p>
    <p style="padding: 0 6vw; font-size:18px; line-height: 2;">Adapun hal yang anda perlu ketahui lebih lanjut adalah: data anda akan diambil demi penelitian lebih lanjut. Apabila anda tidak berkenan untuk memberikan data anda kepada kami maka anda dapat menyamarkan nama anda. Karena keterbatasan data yang dapat diolah oleh perangkat komputasi peneliti maka fokus pada survey ini adalah mengetahui apakah masalah <b>cold start</b> dapat diselesaikan dengan impelentasi Multi Armed Bandits. <b>Jadi jika anda mendapatkan guru meskipun tidak terlalu sesuai personalitasnya maka program akan dianggap berhasil, jika personalitas sesuai dengan guru maka hal tersebut adalah nilai tambah bagi program.</b> Akan ada hadiah jika anda beruntung saat mengerjakan survey ini! <br>Program dibuat menggunakan:</p>
    <b>Python, MAB, SVELTE</b>
    <p>Selamat mengerjakan</p>   
    <hr>
    <h1>1. Data diri</h1>
    <b>Isi data diri anda</b>
    <p>Nama (boleh samaran)</p>
    <input id="name" placeholder="Enter name"/>
    <p>Umur</p>
    <input id="umur" placeholder="Enter age"/>
    <p>Gender<br></p>
    <select class = "gender" id="gender" name="gender">
        <option value="1">Male</option>
        <option value="2">Female</option>
    </select>
    <p>Kontak<br>(dapat berupa ig, email, no hp, dll, dapat dilewati apabila tidak berkenan mendapatkan hadiah undian)</p>
    <input />
    <h1>2. Survey</h1>
    <b>Isi pertanyaan / pernyataan dari skala 1 - 5, satu (1) berarti tidak setuju, lima (5) berarti setuju</b>


</center>


<div class="inside">
{#each Array(40).fill() as _, index (index)}
<center><p>{questions[index] || "No question available for this set."}</p></center>

    <div class="button-container">
        {#each [1, 2, 3, 4, 5] as number}
            <div class="button-group">
                <button
                on:click={() => selectNumber(number, index)}
                style="background-color: {number === selectedNumbers[index] ? getColorForNumber(number) : 'white'};">
                {number}
            </button>
                {#if number === 1}
                    <div class="number-display">Tidak Setuju</div>
                {/if}
                {#if number === 3}
                    <div class="number-display">Netral</div>
                {/if}
                {#if number === 5}
                    <div class="number-display">Setuju</div>
                {/if}
            </div>
        {/each}
    </div>
{/each}
</div>
<center>
    <button class = "submit" on:click={submit}> Submit</button>
</center>
