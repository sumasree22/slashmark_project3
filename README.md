# slashmark_project3
Task 3 :Weather Forecast Website

In this project, you will make a web application to check out the weather forecast for the current day and for the next few days. You will use an API to fetch real-time data and then add it to your application. The user will input his/her location and the weather forecast for the next 5 days will be displayed. In addition, a feature to automatically detect the location can add to the versatility of the project.


==>Creating a weather forecast web application involves several key steps, including setting up a web server, fetching data from a weather API, and creating a user interface to display the weather information. Here's a step-by-step guide to help you build this project:
1. Choose Your Technology Stack
You will need to choose a technology stack for your web application. Here’s a recommended stack:
•	Frontend: HTML, CSS, JavaScript (with a framework like React.js or Vue.js if you prefer)
•	Backend: Node.js with Express.js
•	API: OpenWeatherMap API or any other weather API
•	Geolocation: Browser Geolocation API
2. Set Up Your Development Environment
•	Install Node.js and npm.
•	Set up your project directory and initialize it with npm init.
•	Install necessary dependencies: express, axios (for making HTTP requests), and dotenv (for managing environment variables).
3. Create Your Backend
Your backend will handle API requests to the weather service and serve the frontend files.
server.js:
const express = require('express');
const axios = require('axios');
const path = require('path');
require('dotenv').config();

const app = express();
const port = process.env.PORT || 3000;
const apiKey = process.env.WEATHER_API_KEY;

app.use(express.static(path.join(__dirname, 'public')));

app.get('/weather', async (req, res) => {
    const { lat, lon } = req.query;
    if (!lat || !lon) {
        return res.status(400).json({ error: 'Location coordinates are required' });
    }

    try {
        const response = await axios.get(`https://api.openweathermap.org/data/2.5/forecast`, {
            params: {
                lat,
                lon,
                appid: apiKey,
                units: 'metric' // or 'imperial' for Fahrenheit
            }
        });
        res.json(response.data);
    } catch (error) {
        res.status(500).json({ error: 'Failed to fetch weather data' });
    }
});

app.listen(port, () => {
    console.log(`Server is running on port ${port}`);
});

.env:
WEATHER_API_KEY=your_openweather_api_key
4. Create Your Frontend
The frontend will have an HTML file, a CSS file for styling, and a JavaScript file to handle interactions and fetch data from the backend.
public/index.html:
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather Forecast</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <h1>Weather Forecast</h1>
        <button id="getLocationBtn">Get My Location</button>
        <div id="weatherData"></div>
    </div>
    <script src="app.js"></script>
</body>
</html>

public/styles.css:
body {
    font-family: Arial, sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    margin: 0;
    background-color: #f0f0f0;
}

.container {
    text-align: center;
    background: #fff;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

#weatherData {
    margin-top: 20px;
}
public/app.js:
document.getElementById('getLocationBtn').addEventListener('click', () => {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(showPosition, showError);
    } else {
        alert('Geolocation is not supported by this browser.');
    }
});

function showPosition(position) {
    const lat = position.coords.latitude;
    const lon = position.coords.longitude;

    fetch(`/weather?lat=${lat}&lon=${lon}`)
        .then(response => response.json())
        .then(data => displayWeather(data))
        .catch(error => console.error('Error fetching weather data:', error));
}

function showError(error) {
    let errorText;
    switch(error.code) {
        case error.PERMISSION_DENIED:
            errorText = "User denied the request for Geolocation.";
            break;
        case error.POSITION_UNAVAILABLE:
            errorText = "Location information is unavailable.";
            break;
        case error.TIMEOUT:
            errorText = "The request to get user location timed out.";
            break;
        case error.UNKNOWN_ERROR:
            errorText = "An unknown error occurred.";
            break;
    }
    alert(errorText);
}

function displayWeather(data) {
    const weatherDiv = document.getElementById('weatherData');
    weatherDiv.innerHTML = '';

    data.list.forEach(item => {
        const weatherItem = document.createElement('div');
        weatherItem.className = 'weather-item';
        weatherItem.innerHTML = `
            <h3>${new Date(item.dt_txt).toLocaleDateString()}</h3>
            <p>Temperature: ${item.main.temp} °C</p>
            <p>Weather: ${item.weather[0].description}</p>
        `;
        weatherDiv.appendChild(weatherItem);
    });
}
5. Run Your Application
Start your backend server:
node server.js

1.	Open your browser and navigate to http://localhost:3000.
You should now have a functioning weather forecast web application that can detect the user's location and display the weather forecast for the next 5 days. If you want to deploy this application, consider using platforms like Heroku, Vercel, or Netlify.



