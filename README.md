
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive Workout Calendar</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f4f4f9;
            margin: 0;
            padding: 0;
        }
        .calendar-container {
            margin: 20px auto;
            max-width: 800px;
        }
        .month-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            background-color: #4CAF50;
            color: white;
            padding: 10px;
        }
        .month-header button {
            background: none;
            border: none;
            color: white;
            font-size: 1.2em;
            cursor: pointer;
        }
        .month-header button:hover {
            text-decoration: underline;
        }
        .month-title {
            font-size: 1.5em;
        }
        .calendar {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 5px;
            margin-top: 10px;
        }
        .day {
            padding: 10px;
            border: 1px solid #ddd;
            background-color: #f9f9f9;
            text-align: left;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            position: relative;
            cursor: pointer;
        }
        .day .date {
            font-weight: bold;
        }
        .day textarea {
            width: 100%;
            height: 50px;
            margin-top: 5px;
            font-size: 0.9em;
        }
        .day.completed {
            background-color: #4CAF50;
            color: white;
        }
        .day.completed .date {
            color: white;
        }
        .day.completed textarea {
            background-color: white;
            color: black;
        }
    </style>
</head>
<body>
    <div class="calendar-container">
        <div class="month-header">
            <button id="prev-month">◀</button>
            <div class="month-title" id="month-title"></div>
            <button id="next-month">▶</button>
        </div>
        <div class="calendar" id="calendar"></div>
    </div>

    <script>
        const workoutSplit = ["Push", "Pull", "Legs 1", "Cardio and Abs", "Upperbody", "Legs 2", "Rest"];
        const calendar = document.getElementById("calendar");
        const monthTitle = document.getElementById("month-title");
        const prevMonthButton = document.getElementById("prev-month");
        const nextMonthButton = document.getElementById("next-month");

        let currentDate = new Date(2024, 10); // Start from November 2024
        let workouts = {};

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
            for (let day = 1; day <= daysInMonth; day++) {
                const dateKey = `${year}-${String(month + 1).padStart(2, "0")}-${String(day).padStart(2, "0")}`;
                const dayDiv = document.createElement("div");
                dayDiv.classList.add("day");
                dayDiv.dataset.date = dateKey;

                // Header with date and workout split
                const dateDiv = document.createElement("div");
                dateDiv.classList.add("date");
                dateDiv.textContent = `${day} - ${workoutSplit[(day - 1) % workoutSplit.length]}`;
                dayDiv.appendChild(dateDiv);

                // Text area for workout log
                const textArea = document.createElement("textarea");
                textArea.value = workouts[dateKey]?.log || "";
                textArea.addEventListener("input", () => {
                    workouts[dateKey] = workouts[dateKey] || {};
                    workouts[dateKey].log = textArea.value;
                });
                dayDiv.appendChild(textArea);

                // Click event to mark as completed
                dayDiv.addEventListener("click", () => {
                    dayDiv.classList.toggle("completed");
                    workouts[dateKey] = workouts[dateKey] || {};
                    workouts[dateKey].completed = dayDiv.classList.contains("completed");
                });

                // Mark as completed if already done
                if (workouts[dateKey]?.completed) {
                    dayDiv.classList.add("completed");
                }

                calendar.appendChild(dayDiv);
            }
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