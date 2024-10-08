<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BARS - Записи</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f4f8;
            color: #333;
            margin: 0;
            padding: 0;
        }
        header {
            background-color: #007BFF;
            color: white;
            padding: 15px;
            text-align: center;
        }
        .add-icon {
            display: inline-block;
            font-size: 24px;
            color: #007BFF;
            cursor: pointer;
            margin: 10px 0;
        }
        .container {
            padding: 20px;
        }
        .appointment-list {
            width: 100%;
            border-collapse: collapse;
            margin-bottom: 20px;
        }
        .appointment-list th, .appointment-list td {
            padding: 10px;
            border-bottom: 1px solid #ddd;
        }
        .appointment-list th {
            background-color: #007BFF;
            color: white;
        }
        .soon-appointment {
            background-color: #ffcccc;
        }
        .add-appointment {
            background-color: #007BFF;
            color: white;
            padding: 10px;
            border: none;
            cursor: pointer;
        }
        .form-input {
            margin-bottom: 10px;
            padding: 10px;
            width: 100%;
            max-width: 300px;
        }
        .status-checkbox {
            cursor: pointer;
        }
    </style>
</head>
<body>

<header>
    <h1>BARS - Записи</h1>
</header>

<div class="container">
    <h2>Записи на сегодня</h2>
    <span class="add-icon" onclick="toggleForm()">&#43;</span> <!-- Кнопка добавления записи под заголовком -->
    <table class="appointment-list">
        <thead>
            <tr>
                <th>Клиент</th>
                <th>Время</th>
                <th>Барбер</th>
                <th>Статус</th>
                <th>Действие</th>
            </tr>
        </thead>
        <tbody id="appointmentData">
            <tr>
                <td>Иван Иванов</td>
                <td>10:00</td>
                <td>Лаура</td>
                <td><input type="checkbox" class="status-checkbox"></td>
                <td><button onclick="removeAppointment(this)">Удалить</button></td>
            </tr>
        </tbody>
    </table>

    <div id="appointmentFormContainer" style="display: none;">
        <h2>Добавить новую запись</h2>
        <form id="appointmentForm">
            <input type="text" id="clientName" class="form-input" placeholder="Имя клиента" required>
            <input type="time" id="appointmentTime" class="form-input" required>
            <select id="barberName" class="form-input" required>
                <option value="">Выберите барбера</option>
                <option value="Лаура">Лаура</option>
                <option value="Алмас">Алмас</option>
            </select>
            <button type="submit" class="add-appointment">Добавить запись</button>
        </form>
    </div>
</div>

<script>
    document.getElementById('appointmentForm').addEventListener('submit', function(e) {
        e.preventDefault(); // Останавливаем перезагрузку страницы

        // Получаем данные из формы
        const clientName = document.getElementById('clientName').value;
        const appointmentTime = document.getElementById('appointmentTime').value;
        const barberName = document.getElementById('barberName').value;

        // Создаем новую строку для таблицы
        const newRow = document.createElement('tr');
        newRow.innerHTML = `
            <td>${clientName}</td>
            <td>${appointmentTime}</td>
            <td>${barberName}</td>
            <td><input type="checkbox" class="status-checkbox"></td>
            <td><button onclick="removeAppointment(this)">Удалить</button></td>
        `;

        // Добавляем строку в таблицу
        document.getElementById('appointmentData').appendChild(newRow);

        // Очищаем форму
        document.getElementById('clientName').value = '';
        document.getElementById('appointmentTime').value = '';
        document.getElementById('barberName').value = '';

        // Проверка времени записи для выделения
        checkSoonAppointments();

        // Скрываем форму после добавления
        toggleForm();
    });

    // Функция для удаления записи
    function removeAppointment(button) {
        button.parentElement.parentElement.remove();
    }

    // Функция для переключения видимости формы
    function toggleForm() {
        const formContainer = document.getElementById('appointmentFormContainer');
        if (formContainer.style.display === 'none') {
            formContainer.style.display = 'block';
        } else {
            formContainer.style.display = 'none';
        }
    }

    // Функция для отметки красным тех, у кого запись через 30 минут
    function checkSoonAppointments() {
        const rows = document.querySelectorAll('#appointmentData tr');
        const now = new Date();

        rows.forEach(row => {
            const timeCell = row.querySelector('td:nth-child(2)');
            const appointmentTime = timeCell.innerText;
            const [hours, minutes] = appointmentTime.split(':');
            const appointmentDate = new Date(now.getFullYear(), now.getMonth(), now.getDate(), hours, minutes);

            // Если запись через 30 минут или меньше
            if ((appointmentDate - now) <= 30 * 60 * 1000 && (appointmentDate - now) > 0) {
                row.classList.add('soon-appointment'); // Добавляем класс для подсветки
            } else {
                row.classList.remove('soon-appointment'); // Убираем класс, если время не соответствует
            }
        });
    }

    // Автоматическая проверка каждые 30 секунд
    setInterval(checkSoonAppointments, 30000); // 30000 мс = 30 секунд

    // Проверка при загрузке страницы
    window.onload = checkSoonAppointments;
</script>

</body>
</html>

