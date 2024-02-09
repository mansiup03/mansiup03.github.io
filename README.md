# Vue.js ToDo App

A simple ToDo application built with Vue.js and Axios.

## Overview

This ToDo app allows users to add, delete, and update tasks categorized as "Business" or "Personal". It uses Vue.js for the frontend and Axios for making HTTP requests to a server (assumed to be running locally).

## Features

- Add tasks with a description and category
- Mark tasks as done
- Remove tasks
- Update tasks
- Filter tasks by category
- Displays date of creation to the user


## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) installed
- [Vue CLI](https://cli.vuejs.org/) installed (`npm install -g @vue/cli`)

### Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/your-username/vue-todo-app.git
   
Navigate to the project directory:
cd vue-todo-app

Install dependencies:
npm install

If Axios is not installed, install it:
npm install axios


Run the local JSON server:
The ToDo app uses a local JSON server to store and retrieve data.
Make sure to run db.json using json-server. 

Install it globally if not already installed:
npm install -g json-server

Start the JSON server:
json-server --watch db.json --port 3000 or simply write  json-server --watch db.json
The server will run at http://localhost:3000/

Run the Vue.js app (in a separate terminal window):
npm run serve
Open your browser and visit http://localhost:8080/

Usage:
Enter your name in the greeting section.
Create a ToDo by providing a description and selecting a category.
Use the checkboxes to mark tasks as done.
Update and delete tasks using the provided buttons.
Filter tasks by category using the dropdown menu.
