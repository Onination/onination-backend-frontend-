# onination-backend-frontend-
ONINATION is a simple AI-powered coding learning platform that helps beginners learn programming through interactive challenges, XP rewards, and guided practice.  It is designed to make learning to code easier, more engaging, and more motivating by combining structured challenges with a gamified progression system.
const express = require("express");
const cors = require("cors");

const app = express();
app.use(cors());
app.use(express.json());

// fake database
let users = [];
let challenges = [
  { id: 1, title: "Hello World", xp: 10 },
  { id: 2, title: "Variables", xp: 20 }
];

// HOME
app.get("/", (req, res) => {
  res.send("ONINATION API is running 🚀");
});

// SIGNUP
app.post("/signup", (req, res) => {
  const user = {
    id: users.length + 1,
    email: req.body.email,
    xp: 0
  };

  users.push(user);
  res.json(user);
});

// GET USERS
app.get("/users", (req, res) => {
  res.json(users);
});

// GET CHALLENGES
app.get("/challenges", (req, res) => {
  res.json(challenges);
});

// COMPLETE CHALLENGE
app.post("/complete", (req, res) => {
  const user = users.find(u => u.id === req.body.userId);
  const challenge = challenges.find(c => c.id === req.body.challengeId);

  if (user && challenge) {
    user.xp += challenge.xp;
    res.json({ message: "XP added!", user });
  } else {
    res.status(404).json({ error: "Not found" });
  }
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
  console.log("Server running on port " + PORT);
});
{
  "name": "onination-backend",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {
    "cors": "^2.8.5",
    "express": "^4.18.2"
  },
  "scripts": {
    "start": "node index.js"
  }
}<!DOCTYPE html>
<html>
<head>
  <title>ONINATION</title>
</head>
<body>

  <h1>ONINATION 🚀</h1>

  <input id="email" placeholder="Enter email" />
  <button onclick="signup()">Sign Up</button>

  <h2>Challenges</h2>
  <div id="challenges"></div>

  <h2>Users</h2>
  <div id="users"></div>

<script>
const API = "http://localhost:5000";

// SIGNUP
async function signup() {
  const email = document.getElementById("email").value;

  await fetch(API + "/signup", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ email })
  });

  loadUsers();
}

// LOAD USERS
async function loadUsers() {
  const res = await fetch(API + "/users");
  const data = await res.json();

  document.getElementById("users").innerHTML =
    data.map(u => `<p>${u.email} - XP: ${u.xp}</p>`).join("");
}

// LOAD CHALLENGES
async function loadChallenges() {
  const res = await fetch(API + "/challenges");
  const data = await res.json();

  document.getElementById("challenges").innerHTML =
    data.map(c =>
      `<p>${c.title} (+${c.xp} XP)
        <button onclick="complete(${c.id})">Complete</button>
      </p>`
    ).join("");
}

// COMPLETE
async function complete(id) {
  await fetch(API + "/complete", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ userId: 1, challengeId: id })
  });

  loadUsers();
}

loadChallenges();
loadUsers();
</script>

</body>
</html>
