
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Workout Tracker</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f4f4f9;
            color: #333;
        }

        .container {
            max-width: 100%;
            margin: 0 auto;
            padding: 10px;
            background: white;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            display: flex;
            flex-direction: column;
            height: 100vh;
            box-sizing: border-box;
        }

        .header {
            text-align: center;
            padding: 15px;
            background-color: #4CAF50;
            color: white;
            border-radius: 10px 10px 0 0;
        }

        .header h1 {
            font-size: 1.5em;
            margin: 0;
        }

        .day-view, .month-view {
            flex: 1;
            overflow-y: auto;
            display: none;
            flex-direction: column;
            justify-content: space-between;
        }

        .day-view.active, .month-view.active {
            display: flex;
        }

        .day-details {
            text-align: center;
            margin: 20px 0;
        }

        .day-details h2 {
            font-size: 1.5em;
            margin-bottom: 10px;
        }

        .day-details p {
            font-size: 1.2em;
            color: #555;
        }

        .day-log textarea {
            width: 100%;
            height: 250px;
            font-size: 1.1em;
            padding: 15px;
            margin: 10px 0;
            border: 1px solid #ddd;
            border-radius: 5px;
            resize: none;
            box-sizing: border-box;
        }

        .day-log textarea:focus {
            outline: none;
            border-color: #4CAF50;
        }

        .navigation-buttons {
            display: flex;
            justify-content: space-between;
            margin: 20px 0;
        }

        .navigation-buttons button {
            padding: 15px 25px;
            border: none;
            background-color: #4CAF50;
            color: white;
            font-size: 1em;
            border-radius: 5px;
            cursor: pointer;
        }

        .navigation-buttons button:hover {
            background-color: #45a049;
        }

        .view-toggle {
            text-align: center;
            margin-top: 10px;
        }

        .view-toggle button {
            padding: 15px 25px;
            border: none;
            background-color: #007BFF;
            color: white;
            font-size: 1em;
            border-radius: 5px;
            cursor: pointer;
        }

        .view-toggle button:hover {
            background-color: #0056b3;
        }

        .month-view .calendar {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 10px;
            margin-top: 20px;
        }

        .month-view .calendar .day {
            padding: 15px;
            border: 1px solid #ddd;
            border-radius: 5px;
            background-color: #ffffff;
            text-align: center;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
            cursor: pointer;
        }

        .month-view .calendar .day:hover {
            background-color: #f0f0f0;
        }

        .month-view .calendar .day.completed {
            background-color: #4CAF50;
            color: white;
        }

        @media (max-width: 600px) {
            .day-log textarea {
                height: 200px;
            }

            .navigation-buttons button,
            .view-toggle button {
                padding: 10px 15px;
                font-size: 0.9em;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Workout Tracker</h1>
        </div>

        <div class="day-view active" id="day-view">
            <div class="day-details">
                <h2 id="day-title"></h2>
                <p id="workout-type"></p>
            </div>
            <div class="day-log">
                <textarea id="day-log" placeholder="Write about your workout today..."></textarea>
            </div>
            <div class="navigation-buttons">
                <button id="prev-day">Previous Day</button>
                <button id="next-day">Next Day</button>
            </div>
        </div>

        <div class="month-view" id="month-view">
            <div class="calendar" id="calendar"></div>
        </div>

        <div class="view-toggle">
            <button id="toggle-view">Switch to Month View</button>
        </div>
    </div>

    <script>
        const workoutPlan = [
            "Rest",                          // Sunday
            "Push (Chest/Triceps/Shoulders)", // Monday
            "Pull (Back/Biceps)",            // Tuesday
            "Legs & Abs",                    // Wednesday
            "Upper Body (Chest/Back/Shoulders & Arms)", // Thursday
            "Lower Body (Legs & Cardio)",    // Friday
            "Rest"                           // Saturday
        ];

        const startDate = new Date(2024, 10, 18); // November 18, 2024
        const endDate = new Date(2025, 10, 17); // 1 year later
        let currentDate = startDate;
        let workouts = JSON.parse(localStorage.getItem("workouts")) || {};

        const dayView = document.getElementById("day-view");
        const monthView = document.getElementById("month-view");
        const dayTitle = document.getElementById("day-title");
        const workoutType = document.getElementById("workout-type");
        const dayLog = document.getElementById("day-log");
        const prevDayButton = document.getElementById("prev-day");
        const nextDayButton = document.getElementById("next-day");
        const toggleViewButton = document.getElementById("toggle-view");
        const calendar = document.getElementById("calendar");

        function getWorkoutType(date) {
            const dayOfWeek = date.getDay();
            return workoutPlan[dayOfWeek];
        }

        function formatDate(date) {
            return date.toISOString().split("T")[0];
        }

        function updateDayView() {
            const dateKey = formatDate(currentDate);
            dayTitle.textContent = currentDate.toDateString();
            workoutType.textContent = getWorkoutType(currentDate);
            dayLog.value = workouts[dateKey]?.log || "";
        }

        function saveWorkoutLog() {
            const dateKey = formatDate(currentDate);
            workouts[dateKey] = workouts[dateKey] || {};
            workouts[dateKey].log = dayLog.value;
            localStorage.setItem("workouts", JSON.stringify(workouts));
        }

        function switchDay(offset) {
            currentDate.setDate(currentDate.getDate() + offset);
            if (currentDate < startDate) currentDate = startDate;
            if (currentDate > endDate) currentDate = endDate;
            updateDayView();
        }

        function updateMonthView() {
            calendar.innerHTML = "";
            const year = currentDate.getFullYear();
            const month = currentDate.getMonth();
            const daysInMonth = new Date(year, month + 1, 0).getDate();

            for (let day = 1; day <= daysInMonth; day++) {
                const date = new Date(year, month, day);
                const dateKey = formatDate(date);

                const dayDiv = document.createElement("div");
                dayDiv.classList.add("day");
                dayDiv.textContent = day;

                if (workouts[dateKey]?.log) dayDiv.classList.add("completed");

                dayDiv.addEventListener("click", () => {
                    currentDate = date;
                    toggleView();
                    updateDayView();
                });

                calendar.appendChild(dayDiv);
            }
        }

        function toggleView() {
            if (dayView.classList.contains("active")) {
                dayView.classList.remove("active");
                monthView.classList.add("active");
                toggleViewButton.textContent = "Switch to Day View";
                updateMonthView();
            } else {
                monthView.classList.remove("active");
                dayView.classList.add("active");
                toggleViewButton.textContent = "Switch to Month View";
            }
        }

        dayLog.addEventListener("input", saveWorkoutLog);
        prevDayButton.addEventListener("click", () => switchDay(-1));
        nextDayButton.addEventListener("click", () => switchDay(1));
        toggleViewButton.addEventListener("click", toggleView);

        updateDayView();
    </script>
</body>
</html>