
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

        .day-view {
            flex: 1;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
        }

        .day-details {
            text-align: center;
            margin: 20px 0;
        }

        .day-details h2 {
            font-size: 1.2em;
            margin-bottom: 10px;
        }

        .day-details p {
            font-size: 1em;
            color: #555;
        }

        .day-log textarea {
            width: 100%;
            height: 200px;
            font-size: 1em;
            padding: 10px;
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
            margin: 10px 0;
        }

        .navigation-buttons button {
            padding: 10px 15px;
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
            padding: 10px 15px;
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

        @media (max-width: 600px) {
            .day-log textarea {
                height: 150px;
            }

            .navigation-buttons button,
            .view-toggle button {
                padding: 10px 10px;
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

        <div class="day-view" id="day-view">
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

        // Elements
        const dayTitle = document.getElementById("day-title");
        const workoutType = document.getElementById("workout-type");
        const dayLog = document.getElementById("day-log");
        const prevDayButton = document.getElementById("prev-day");
        const nextDayButton = document.getElementById("next-day");

        // Helper Functions
        function getWorkoutType(date) {
            // Get the day of the week (0 = Sunday, 1 = Monday, ... 6 = Saturday)
            const dayOfWeek = date.getDay();
            return workoutPlan[dayOfWeek];
        }

        function formatDate(date) {
            return date.toISOString().split("T")[0];
        }

        function updateDayView() {
            const dateKey = formatDate(currentDate);
            dayTitle.textContent = currentDate.toDateString();
            workoutType.textContent = getWorkoutType(currentDate); // Correctly map the workout plan to the day of the week
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

            // Prevent navigating before start or after end
            if (currentDate < startDate) currentDate = startDate;
            if (currentDate > endDate) currentDate = endDate;

            updateDayView();
        }

        // Event Listeners
        dayLog.addEventListener("input", saveWorkoutLog);
        prevDayButton.addEventListener("click", () => switchDay(-1));
        nextDayButton.addEventListener("click", () => switchDay(1));

        // Initialize
        updateDayView();
    </script>
</body>
</html>