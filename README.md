
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Yearly Workout Calendar</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f4f4f9;
            margin: 0;
            padding: 0;
        }
        .calendar {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 20px;
            margin: 20px auto;
            max-width: 1200px;
        }
        .month {
            border: 1px solid #ddd;
            background-color: #fff;
            padding: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }
        .month h3 {
            margin: 0;
            padding: 10px 0;
            background-color: #4CAF50;
            color: white;
        }
        .days {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 5px;
            font-size: 0.9em;
        }
        .day {
            padding: 10px;
            border: 1px solid #ddd;
            background-color: #f9f9f9;
            cursor: pointer;
            text-align: center;
        }
        .day:hover {
            background-color: #e0f7fa;
        }
        .day.selected {
            background-color: #4CAF50;
            color: white;
        }
        #workout-modal {
            display: none;
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: white;
            padding: 20px;
            border: 1px solid #ddd;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
            z-index: 1000;
        }
        #workout-modal textarea {
            width: 100%;
            height: 100px;
        }
        #workout-modal button {
            margin-top: 10px;
        }
        #overlay {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            z-index: 999;
        }
    </style>
</head>
<body>
    <h1>Yearly Workout Calendar</h1>
    <div class="calendar" id="calendar"></div>

    <div id="overlay"></div>

    <div id="workout-modal">
        <h3>Workout Log for <span id="selected-date"></span></h3>
        <textarea id="workout-log" placeholder="Write your workout here..."></textarea>
        <button onclick="saveWorkout()">Save</button>
        <button onclick="closeModal()">Cancel</button>
    </div>

    <script>
        const calendarContainer = document.getElementById('calendar');
        const workoutModal = document.getElementById('workout-modal');
        const overlay = document.getElementById('overlay');
        const selectedDateDisplay = document.getElementById('selected-date');
        const workoutLog = document.getElementById('workout-log');
        let selectedDate = null;
        let workouts = {};

        // Function to generate the yearly calendar
        function generateCalendar(year) {
            const months = [
                "January", "February", "March", "April", "May", "June",
                "July", "August", "September", "October", "November", "December"
            ];
            calendarContainer.innerHTML = '';
            months.forEach((month, index) => {
                const monthDiv = document.createElement('div');
                monthDiv.classList.add('month');

                const monthTitle = document.createElement('h3');
                monthTitle.textContent = month;
                monthDiv.appendChild(monthTitle);

                const daysDiv = document.createElement('div');
                daysDiv.classList.add('days');

                const daysInMonth = new Date(year, index + 1, 0).getDate();

                for (let day = 1; day <= daysInMonth; day++) {
                    const dayDiv = document.createElement('div');
                    dayDiv.classList.add('day');
                    dayDiv.textContent = day;
                    dayDiv.dataset.date = `${year}-${String(index + 1).padStart(2, '0')}-${String(day).padStart(2, '0')}`;

                    // Click event for opening the workout modal
                    dayDiv.addEventListener('click', () => {
                        openModal(dayDiv.dataset.date);
                    });

                    daysDiv.appendChild(dayDiv);
                }

                monthDiv.appendChild(daysDiv);
                calendarContainer.appendChild(monthDiv);
            });
        }

        // Function to open the workout modal
        function openModal(date) {
            selectedDate = date;
            selectedDateDisplay.textContent = date;
            workoutLog.value = workouts[date] || '';
            workoutModal.style.display = 'block';
            overlay.style.display = 'block';
        }

        // Function to close the workout modal
        function closeModal() {
            workoutModal.style.display = 'none';
            overlay.style.display = 'none';
        }

        // Function to save the workout log
        function saveWorkout() {
            if (selectedDate) {
                workouts[selectedDate] = workoutLog.value;
                document.querySelectorAll(`.day[data-date='${selectedDate}']`)[0].classList.add('selected');
            }
            closeModal();
        }

        // Initialize the calendar
        generateCalendar(new Date().getFullYear());
    </script>
</body>
</html>