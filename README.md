
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Workout Calendar</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            text-align: center;
            background-color: #f4f4f9;
            margin: 0;
            padding: 0;
        }

        .calendar-container {
            margin: 20px auto;
            max-width: 1000px;
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        }

        .month-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            padding: 10px;
            border-radius: 5px;
            background-color: #4CAF50;
            color: white;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }

        .month-header button {
            background: none;
            border: none;
            color: white;
            font-size: 1.5em;
            cursor: pointer;
        }

        .month-header button:hover {
            text-decoration: underline;
        }

        .month-title {
            font-size: 1.8em;
        }

        .calendar {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 10px;
            margin-top: 20px;
        }

        .day {
            position: relative;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
            background-color: #ffffff;
            text-align: left;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }

        .day:hover {
            background-color: #f9f9f9;
            cursor: pointer;
        }

        .day.completed {
            background-color: #4CAF50;
            color: white;
        }

        .day.completed:hover {
            background-color: #45a049;
        }

        .day .date {
            font-weight: bold;
            font-size: 1.2em;
            margin-bottom: 5px;
        }

        .day textarea {
            width: 100%;
            height: 60px;
            margin-top: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
            font-size: 0.9em;
            padding: 5px;
            resize: none;
        }

        .day textarea:focus {
            outline: none;
            border-color: #4CAF50;
        }

        .instructions {
            margin-bottom: 20px;
            color: #555;
            font-size: 1.1em;
        }
    </style>
</head>
<body>
    <div class="calendar-container">
        <div class="month-header">
            <button id="prev-month">◀</button>
            <div id="month-title"></div>
            <button id="next-month">▶</button>
        </div>
        <div class="instructions">Click a day to add notes or mark it as completed. Use the arrows to navigate months.</div>
        <div class="calendar" id="calendar"></div>
    </div>

    <script>
        const workoutSplit = ["Push", "Pull", "Legs 1", "Cardio and Abs", "Upperbody", "Legs 2", "Rest"];
        const calendar = document.getElementById("calendar");
        const monthTitle = document.getElementById("month-title");
        const prevMonthButton = document.getElementById("prev-month");
        const nextMonthButton = document.getElementById("next-month");

        let currentDate = new Date(2024, 10, 18); // Start from November 18, 2024
        let workouts = JSON.parse(localStorage.getItem('workouts')) || {};

        // Generate the calendar for the current month
        function generateCalendar(year, month) {
            calendar.innerHTML = "";
            const firstDay = new Date(year, month, 1).getDay();
            const daysInMonth = new Date(year, month + 1, 0).getDate();
            const monthName = new Date(year, month).toLocaleString("default", { month: "long" });
            monthTitle.textContent = `${monthName} ${year}`;

            // Fill in empty days for alignment
            for (let i = 0; i < firstDay; i++) {
                const emptyDiv = document.createElement("div");
                calendar.appendChild(emptyDiv);
            }

            // Generate days
            let workoutIndex = month === 10 && year === 2024 ? 0 : null; // Start workout split only from Nov 18
            for (let day = 1; day <= daysInMonth; day++) {
                const dateKey = `${year}-${String(month + 1).padStart(2, "0")}-${String(day).padStart(2, "0")}`;
                const dayDiv = document.createElement("div");
                dayDiv.classList.add("day");
                dayDiv.dataset.date = dateKey;

                // Workout starts from November 18
                if (year === 2024 && month === 10 && day >= 18) {
                    workoutIndex = workoutIndex === null ? 0 : workoutIndex; // Start split from "Push"
                } else if (workoutIndex !== null) {
                    workoutIndex++;
                }

                // Header with date and workout split
                const dateDiv = document.createElement("div");
                dateDiv.classList.add("date");
                const workoutName = workoutIndex !== null ? workoutSplit[workoutIndex % workoutSplit.length] : '';
                dateDiv.textContent = `${day} ${workoutName ? '- ' + workoutName : ''}`;
                dayDiv.appendChild(dateDiv);

                // Text area for workout log
                const textArea = document.createElement("textarea");
                textArea.value = workouts[dateKey]?.log || '';
                textArea.addEventListener("input", () => {
                    workouts[dateKey] = workouts[dateKey] || {};
                    workouts[dateKey].log = textArea.value;
                    saveWorkouts();
                });
                dayDiv.appendChild(textArea);

                // Click event to mark as completed
                dayDiv.addEventListener("click", () => {
                    dayDiv.classList.toggle("completed");
                    workouts[dateKey] = workouts[dateKey] || {};
                    workouts[dateKey].completed = dayDiv.classList.contains("completed");
                    saveWorkouts();
                });

                // Mark as completed if already done
                if (workouts[dateKey]?.completed) {
                    dayDiv.classList.add("completed");
                }

                calendar.appendChild(dayDiv);
            }
        }

        // Save workouts to local storage
        function saveWorkouts() {
            localStorage.setItem('workouts', JSON.stringify(workouts));
        }

        // Navigate to the previous month
        prevMonthButton.addEventListener("click", () => {
            currentDate.setMonth(currentDate.getMonth() - 1);
            generateCalendar(currentDate.getFullYear(), currentDate.getMonth());
        });

        // Navigate to the next month
        nextMonthButton.addEventListener("click", () => {
            currentDate.setMonth(currentDate.getMonth() + 1);
            generateCalendar(currentDate.getFullYear(), currentDate.getMonth());
        });

        // Initialize the calendar
        generateCalendar(currentDate.getFullYear(), currentDate.getMonth());
    </script>
</body>
</html>