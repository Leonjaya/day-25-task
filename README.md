# day-25-task
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Todo App</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <h1>Todo App</h1>

        <!-- Form to create a new Todo -->
        <div class="todo-form">
            <input type="text" id="todo-name" placeholder="Enter Task Name">
            <textarea id="todo-description" placeholder="Enter Task Description"></textarea>
            <button id="add-todo-btn">Add Todo</button>
        </div>

        <!-- Filter for displaying todos -->
        <div class="filter">
            <label for="status-filter">Filter by Status: </label>
            <select id="status-filter">
                <option value="all">All</option>
                <option value="not_completed">Not Completed</option>
                <option value="completed">Completed</option>
            </select>
        </div>

        <!-- List of Todos -->
        <div id="todo-list" class="todo-list"></div>
    </div>

    <script src="script.js"></script>
</body>
</html>

body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f4;
    padding: 20px;
}

.container {
    max-width: 600px;
    margin: 0 auto;
}

.todo-form {
    margin-bottom: 20px;
}

.todo-form input,
.todo-form textarea {
    width: 100%;
    padding: 10px;
    margin-bottom: 10px;
    border: 1px solid #ccc;
    border-radius: 4px;
}

.todo-form button {
    width: 100%;
    padding: 10px;
    background-color: #28a745;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

.todo-form button:hover {
    background-color: #218838;
}

.todo-list .todo-card {
    background-color: white;
    border: 1px solid #ddd;
    border-radius: 4px;
    padding: 15px;
    margin-bottom: 10px;
    position: relative;
}

.todo-list .todo-card h3 {
    margin: 0;
    font-size: 18px;
}

.todo-list .todo-card p {
    margin: 5px 0;
}

.todo-list .todo-card .status {
    cursor: pointer;
    color: #007bff;
}

.todo-list .todo-card .actions {
    position: absolute;
    top: 15px;
    right: 15px;
}

.todo-list .todo-card .actions button {
    background-color: transparent;
    border: none;
    cursor: pointer;
    color: #007bff;
    margin-left: 5px;
}

.todo-list .todo-card .actions button:hover {
    color: #0056b3;
}

.filter {
    margin-bottom: 20px;
}

document.addEventListener('DOMContentLoaded', () => {
    let todos = [];

    const todoList = document.getElementById('todo-list');
    const todoName = document.getElementById('todo-name');
    const todoDescription = document.getElementById('todo-description');
    const addTodoBtn = document.getElementById('add-todo-btn');
    const statusFilter = document.getElementById('status-filter');

    addTodoBtn.addEventListener('click', () => {
        const name = todoName.value.trim();
        const description = todoDescription.value.trim();
        if (name && description) {
            const newTodo = {
                id: Date.now(),
                name: name,
                description: description,
                status: 'not_completed'
            };
            todos.push(newTodo);
            renderTodos();
            todoName.value = '';
            todoDescription.value = '';
        }
    });

    const renderTodos = () => {
        todoList.innerHTML = '';
        const filteredTodos = todos.filter(todo => {
            if (statusFilter.value === 'all') return true;
            return todo.status === statusFilter.value;
        });

        filteredTodos.forEach(todo => {
            const todoCard = document.createElement('div');
            todoCard.className = 'todo-card';
            todoCard.setAttribute('data-id', todo.id);

            todoCard.innerHTML = `
                <h3>${todo.name}</h3>
                <p>${todo.description}</p>
                <span class="status">${todo.status === 'not_completed' ? 'Not Completed' : 'Completed'}</span>
                <div class="actions">
                    <button class="edit-btn">Edit</button>
                    <button class="delete-btn">Delete</button>
                </div>
            `;

            todoCard.querySelector('.status').addEventListener('click', () => {
                const dropdown = document.createElement('select');
                dropdown.innerHTML = `
                    <option value="not_completed">Not Completed</option>
                    <option value="completed">Completed</option>
                `;
                dropdown.value = todo.status;
                dropdown.addEventListener('change', (e) => {
                    todo.status = e.target.value;
                    renderTodos();
                });

                todoCard.querySelector('.status').replaceWith(dropdown);
            });

            todoCard.querySelector('.edit-btn').addEventListener('click', () => {
                const newName = prompt('Edit Task Name:', todo.name);
                const newDescription = prompt('Edit Task Description:', todo.description);
                if (newName && newDescription) {
                    todo.name = newName;
                    todo.description = newDescription;
                    renderTodos();
                }
            });

            todoCard.querySelector('.delete-btn').addEventListener('click', () => {
                todos = todos.filter(t => t.id !== todo.id);
                renderTodos();
            });

            todoList.appendChild(todoCard);
        });
    };

    statusFilter.addEventListener('change', renderTodos);

    renderTodos();
});

