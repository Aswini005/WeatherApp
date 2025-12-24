
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Weather App</title>

<!-- Font Awesome -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
    font-family:'Poppins', sans-serif;
}
body{
    height:100vh;
    background: linear-gradient(135deg,#00feba,#5b548a);
    display:flex;
    justify-content:center;
    align-items:center;
}

/* CONTAINER (NO SHINE) */
.container{
    width:380px;
    background: rgba(255,255,255,0.15);
    backdrop-filter: blur(15px);
    border-radius:25px;
    padding:30px;
    color:#fff;
    box-shadow: 0 10px 25px rgba(0,0,0,0.25); /* simple shadow */
}

/* Search */
.search{
    display:flex;
    gap:10px;
}
.search input{
    flex:1;
    padding:12px 18px;
    border-radius:30px;
    border:none;
    outline:none;
}
.search button{
    width:50px;
    height:50px;
    border-radius:50%;
    border:none;
    cursor:pointer;
}

/* Weather */
.weather{
    text-align:center;
    margin-top:20px;
}
.weather-icon{
    width:120px;
}
.temp{
    font-size:60px;
}
.city{
    font-size:30px;
}

/* Details */
.details{
    display:flex;
    justify-content:space-between;
    margin-top:25px;
}
.col{
    display:flex;
    align-items:center;
    gap:8px;
}
.col img{
    width:30px;
}

/* Forecast */
.forecast{
    display:flex;
    justify-content:space-between;
    margin-top:25px;
}
.day{
    text-align:center;
    font-size:14px;
}
.day img{
    width:35px;
}
.error{
    color:#ffb3b3;
    margin-top:10px;
}
</style>
</head>

<body>
<div class="container">

    <!-- Search -->
    <div class="search">
        <input id="city" type="text" placeholder="Enter city name">
        <button id="btn"><i class="fa fa-search"></i></button>
    </div>

    <!-- Weather -->
    <div class="weather">
        <img src="" class="weather-icon">
        <h1 class="temp">--°C</h1>
        <h2 class="city">City</h2>

        <div class="details">
            <div class="col">
                <img src="https://cdn-icons-png.flaticon.com/512/728/728093.png">
                <div>
                    <p class="humidity">--%</p>
                    <small>Humidity</small>
                </div>
            </div>
            <div class="col">
                <img src="https://cdn-icons-png.flaticon.com/512/959/959711.png">
                <div>
                    <p class="wind">-- km/h</p>
                    <small>Wind</small>
                </div>
            </div>
        </div>
    </div>

    <!-- Forecast -->
    <div class="forecast" id="forecast"></div>
    <div class="error" id="error"></div>

</div>

<script>
const apiKey = "121e89439085483e6b2907da4ac81b0c";

const searchBtn = document.getElementById("btn");
const cityInput = document.getElementById("city");
const forecastBox = document.getElementById("forecast");
const errorBox = document.getElementById("error");
const icon = document.querySelector(".weather-icon");

async function getWeather(city="Vellore"){
    errorBox.textContent="";
    forecastBox.innerHTML="";

    try{
        const res = await fetch(
          `https://api.openweathermap.org/data/2.5/weather?q=${city}&units=metric&appid=${apiKey}`
        );
        if(!res.ok) throw new Error("City not found");
        const data = await res.json();

        document.querySelector(".temp").innerHTML = Math.round(data.main.temp)+"°C";
        document.querySelector(".city").innerHTML = data.name;
        document.querySelector(".humidity").innerHTML = data.main.humidity+"%";
        document.querySelector(".wind").innerHTML = data.wind.speed+" km/h";
        icon.src = `https://openweathermap.org/img/wn/${data.weather[0].icon}@2x.png`;

        const forecastRes = await fetch(
          `https://api.openweathermap.org/data/2.5/forecast?q=${city}&units=metric&appid=${apiKey}`
        );
        const forecastData = await forecastRes.json();

        const days = forecastData.list.filter(item => item.dt_txt.includes("12:00:00"));

        days.forEach(day=>{
            const date = new Date(day.dt_txt);
            const div = document.createElement("div");
            div.className="day";
            div.innerHTML = `
              <p>${date.toLocaleDateString('en-US',{weekday:'short'})}</p>
              <img src="https://openweathermap.org/img/wn/${day.weather[0].icon}.png">
              <p>${Math.round(day.main.temp)}°C</p>
            `;
            forecastBox.appendChild(div);
        });

    }catch(err){
        errorBox.textContent = err.message;
    }
}

searchBtn.onclick = ()=> getWeather(cityInput.value);
cityInput.addEventListener("keyup",e=>{
    if(e.key==="Enter") getWeather(cityInput.value);
});

getWeather();
</script>
</body>
</html>
