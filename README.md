<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Погода в странах мира</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(135deg, #74b9ff 0%, #0984e3 100%);
            padding: 20px;
            min-height: 100vh;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: white;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            overflow: hidden;
        }

        header {
            background: linear-gradient(135deg, #2d3436 0%, #636e72 100%);
            color: white;
            padding: 30px;
            text-align: center;
        }

        h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
        }

        .subtitle {
            font-size: 1.2em;
            opacity: 0.9;
        }

        .controls {
            background: #dfe6e9;
            padding: 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 15px;
        }

        .search-box {
            display: flex;
            gap: 10px;
        }

        input[type="text"] {
            padding: 12px;
            border: 2px solid #b2bec3;
            border-radius: 8px;
            font-size: 16px;
            width: 300px;
        }

        button {
            padding: 12px 24px;
            background: #00b894;
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            transition: background 0.3s;
        }

        button:hover {
            background: #00a085;
        }

        .filters {
            display: flex;
            gap: 15px;
            align-items: center;
        }

        select {
            padding: 12px;
            border: 2px solid #b2bec3;
            border-radius: 8px;
            font-size: 16px;
            background: white;
        }

        .weather-table {
            width: 100%;
            border-collapse: collapse;
        }

        .weather-table th {
            background: #2d3436;
            color: white;
            padding: 20px;
            text-align: left;
            font-size: 16px;
            position: sticky;
            top: 0;
        }

        .weather-table td {
            padding: 18px;
            border-bottom: 1px solid #dfe6e9;
            font-size: 15px;
        }

        .weather-table tr:hover {
            background: #f1f2f6;
            transform: translateY(-2px);
            transition: all 0.3s ease;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }

        .country-flag {
            width: 30px;
            height: 20px;
            margin-right: 10px;
            border-radius: 3px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.2);
        }

        .temperature {
            font-weight: bold;
            font-size: 18px;
        }

        .temp-hot { color: #e17055; }
        .temp-warm { color: #fdcb6e; }
        .temp-cool { color: #00b894; }
        .temp-cold { color: #74b9ff; }

        .weather-icon {
            font-size: 20px;
            margin-right: 8px;
        }

        .condition {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .humidity, .wind {
            color: #636e72;
            font-size: 14px;
        }

        .last-updated {
            background: #dfe6e9;
            padding: 15px;
            text-align: center;
            color: #636e72;
            font-size: 14px;
        }

        @media (max-width: 768px) {
            .controls {
                flex-direction: column;
                align-items: stretch;
            }
            
            input[type="text"] {
                width: 100%;
            }
            
            .search-box, .filters {
                width: 100%;
                justify-content: center;
            }
            
            .weather-table {
                font-size: 14px;
            }
            
            .weather-table th,
            .weather-table td {
                padding: 12px 8px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>🌍 Погода в странах мира</h1>
            <div class="subtitle">Актуальная информация о погодных условиях</div>
        </header>

        <div class="controls">
            <div class="search-box">
                <input type="text" id="searchInput" placeholder="Поиск страны или города...">
                <button onclick="searchWeather()">🔍 Поиск</button>
            </div>
            <div class="filters">
                <select id="continentFilter" onchange="filterTable()">
                    <option value="all">Все континенты</option>
                    <option value="Европа">Европа</option>
                    <option value="Азия">Азия</option>
                    <option value="Северная Америка">Северная Америка</option>
                    <option value="Южная Америка">Южная Америка</option>
                    <option value="Африка">Африка</option>
                    <option value="Австралия и Океания">Австралия и Океания</option>
                </select>
                <select id="seasonFilter" onchange="filterTable()">
                    <option value="all">Все сезоны</option>
                    <option value="лето">Лето</option>
                    <option value="зима">Зима</option>
                    <option value="весна">Весна</option>
                    <option value="осень">Осень</option>
                </select>
            </div>
        </div>

        <div class="table-container">
            <table class="weather-table">
                <thead>
                    <tr>
                        <th>Страна</th>
                        <th>Столица</th>
                        <th>Температура</th>
                        <th>Погодные условия</th>
                        <th>Влажность</th>
                        <th>Ветер</th>
                        <th>Континент</th>
                        <th>Сезон</th>
                    </tr>
                </thead>
                <tbody id="weatherTableBody">
                    <!-- Данные будут загружены через JavaScript -->
                </tbody>
            </table>
        </div>

        <div class="last-updated">
            Последнее обновление: <span id="updateTime"></span>
        </div>
    </div>

    <script>
        // Данные о погоде в странах мира
        const weatherData = [
            {
                country: "Россия",
                capital: "Москва",
                temperature: -5,
                condition: "Снег",
                humidity: 85,
                wind: 12,
                continent: "Европа",
                season: "зима",
                icon: "❄️"
            },
            {
                country: "США",
                capital: "Вашингтон",
                temperature: 8,
                condition: "Облачно",
                humidity: 65,
                wind: 18,
                continent: "Северная Америка",
                season: "зима",
                icon: "☁️"
            },
            {
                country: "Бразилия",
                capital: "Бразилиа",
                temperature: 32,
                condition: "Солнечно",
                humidity: 45,
                wind: 8,
                continent: "Южная Америка",
                season: "лето",
                icon: "☀️"
            },
            {
                country: "Япония",
                capital: "Токио",
                temperature: 12,
                condition: "Дождь",
                humidity: 75,
                wind: 15,
                continent: "Азия",
                season: "весна",
                icon: "🌧️"
            },
            {
                country: "Австралия",
                capital: "Канберра",
                temperature: 28,
                condition: "Ясно",
                humidity: 40,
                wind: 20,
                continent: "Австралия и Океания",
                season: "лето",
                icon: "☀️"
            },
            {
                country: "Египет",
                capital: "Каир",
                temperature: 25,
                condition: "Солнечно",
                humidity: 30,
                wind: 10,
                continent: "Африка",
                season: "зима",
                icon: "☀️"
            },
            {
                country: "Германия",
                capital: "Берлин",
                temperature: 3,
                condition: "Туман",
                humidity: 90,
                wind: 5,
                continent: "Европа",
                season: "зима",
                icon: "🌫️"
            },
            {
                country: "Канада",
                capital: "Оттава",
                temperature: -12,
                condition: "Метель",
                humidity: 80,
                wind: 25,
                continent: "Северная Америка",
                season: "зима",
                icon: "🌨️"
            },
            {
                country: "Индия",
                capital: "Нью-Дели",
                temperature: 22,
                condition: "Ясно",
                humidity: 55,
                wind: 12,
                continent: "Азия",
                season: "весна",
                icon: "☀️"
            },
            {
                country: "Франция",
                capital: "Париж",
                temperature: 6,
                condition: "Пасмурно",
                humidity: 70,
                wind: 14,
                continent: "Европа",
                season: "зима",
                icon: "☁️"
            },
            {
                country: "ЮАР",
                capital: "Кейптаун",
                temperature: 30,
                condition: "Жарко",
                humidity: 25,
                wind: 15,
                continent: "Африка",
                season: "лето",
                icon: "🔥"
            },
            {
                country: "Аргентина",
                capital: "Буэнос-Айрес",
                temperature: 29,
                condition: "Солнечно",
                humidity: 50,
                wind: 10,
                continent: "Южная Америка",
                season: "лето",
                icon: "☀️"
            },
            {
                country: "Китай",
                capital: "Пекин",
                temperature: 5,
                condition: "Смог",
                humidity: 60,
                wind: 8,
                continent: "Азия",
                season: "зима",
                icon: "🌫️"
            },
            {
                country: "Италия",
                capital: "Рим",
                temperature: 10,
                condition: "Переменная облачность",
                humidity: 65,
                wind: 12,
                continent: "Европа",
                season: "весна",
                icon: "⛅"
            },
            {
                country: "Мексика",
                capital: "Мехико",
                temperature: 20,
                condition: "Ясно",
                humidity: 45,
                wind: 10,
                continent: "Северная Америка",
                season: "весна",
                icon: "☀️"
            }
        ];

        // Функция для определения класса температуры
        function getTempClass(temp) {
            if (temp >= 30) return 'temp-hot';
            if (temp >= 20) return 'temp-warm';
            if (temp >= 10) return 'temp-cool';
            return 'temp-cold';
        }

        // Функция для отображения таблицы
        function renderTable(data) {
            const tbody = document.getElementById('weatherTableBody');
            tbody.innerHTML = '';

            data.forEach(item => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>
                        <img src="https://flagcdn.com/w40/${getCountryCode(item.country)}.png" 
                             alt="${item.country}" class="country-flag">
                        ${item.country}
                    </td>
                    <td>${item.capital}</td>
                    <td class="temperature ${getTempClass(item.temperature)}">
                        ${item.temperature > 0 ? '+' : ''}${item.temperature}°C
                    </td>
                    <td>
                        <div class="condition">
                            <span class="weather-icon">${item.icon}</span>
                            ${item.condition}
                        </div>
                    </td>
                    <td class="humidity">${item.humidity}%</td>
                    <td class="wind">${item.wind} км/ч</td>
                    <td>${item.continent}</td>
                    <td>${item.season}</td>
                `;
                tbody.appendChild(row);
            });
        }

        // Функция для получения кода страны (упрощенная версия)
        function getCountryCode(country) {
            const countryCodes = {
                'Россия': 'ru',
                'США': 'us',
                'Бразилия': 'br',
                'Япония': 'jp',
                'Австралия': 'au',
                'Египет': 'eg',
                'Германия': 'de',
                'Канада': 'ca',
                'Индия': 'in',
                'Франция': 'fr',
                'ЮАР': 'za',
                'Аргентина': 'ar',
                'Китай': 'cn',
                'Италия': 'it',
                'Мексика': 'mx'
            };
            return countryCodes[country] || 'un';
        }

        // Функция поиска
        function searchWeather() {
            const searchTerm = document.getElementById('searchInput').value.toLowerCase();
            const filteredData = weatherData.filter(item => 
                item.country.toLowerCase().includes(searchTerm) ||
                item.capital.toLowerCase().includes(searchTerm)
            );
            renderTable(filteredData);
        }

        // Функция фильтрации
        function filterTable() {
            const continentFilter = document.getElementById('continentFilter').value;
            const seasonFilter = document.getElementById('seasonFilter').value;

            let filteredData = weatherData;

            if (continentFilter !== 'all') {
                filteredData = filteredData.filter(item => item.continent === continentFilter);
            }

            if (seasonFilter !== 'all') {
                filteredData = filteredData.filter(item => item.season === seasonFilter);
            }

            renderTable(filteredData);
        }

        // Функция обновления времени
        function updateTime() {
            const now = new Date();
            const options = { 
                year: 'numeric', 
                month: 'long', 
                day: 'numeric',
                hour: '2-digit',
                minute: '2-digit',
                second: '2-digit'
            };
            document.getElementById('updateTime').textContent = 
                now.toLocaleDateString('ru-RU', options);
        }

        // Инициализация при загрузке страницы
        document.addEventListener('DOMContentLoaded', function() {
            renderTable(weatherData);
            updateTime();
            
            // Обновляем время каждую минуту
            setInterval(updateTime, 60000);
            
            // Поиск при нажатии Enter
            document.getElementById('searchInput').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    searchWeather();
                }
            });
        });
    </script>
</body>
</html>
