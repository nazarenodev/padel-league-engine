
# Padel League Engine

The backend service for "Padel Rivals," a social-gamified app for Padel players, featuring rankings, leaderboards, and challenges. This project is designed to showcase a modern, scalable, and resilient system architecture.

---

## Core Concepts

This system is built around a few core concepts:

* **Users:** Players who can challenge each other.
* **Challenges:** An invitation from one player to another for a match.
* **Matches:** A confirmed game that has a lifecycle from `scheduled` to `completed`.
* **Rankings & Leaderboards:** A system to track player skill and standing.
* **Achievements:** Badges that players can earn by completing specific goals.

---

## Feature Overview & API Endpoints

This section outlines the primary features and the API endpoints that will be built to support them.

### 1. User Management

Handles player registration and profile management.

* `POST /users`
    * **Action:** Registers a new player.
    * **Body:** `{ "name": "...", "email": "...", "password": "...", "skillLevel": 3 }`

* `GET /users/{userId}`
    * **Action:** Retrieves a player's public profile, including their stats and badges.

### 2. Match Lifecycle

Manages the flow of a game from a challenge to a confirmed result.

* `POST /challenges`
    * **Action:** A player challenges another player to a match.
    * **Body:** `{ "opponentId": "...", "proposedDate": "..." }`

* `PUT /challenges/{challengeId}`
    * **Action:** The opponent accepts or declines a pending challenge.
    * **Body:** `{ "status": "accepted" | "declined" }`

* `POST /matches/{matchId}/results`
    * **Action:** One of the match players submits the final score. This moves the match into a `pending_confirmation` state.
    * **Body:** `{ "score": [11, 8] }`

* `POST /matches/{matchId}/confirmations`
    * **Action:** The other player confirms the submitted score, moving the match to `completed`.

### 3. Rankings and Social Features

* `GET /leaderboard`
    * **Action:** Retrieves the top N players for the main leaderboard.

* `GET /users/{userId}/matches`
    * **Action:** Retrieves a player's match history.

---

## System Architecture Highlights

### Asynchronous Workflows

To ensure the system is responsive and resilient, critical updates happen in the background using a message queue and worker services. The primary event is `MatchCompleted`.

**When a match is confirmed and completed, the following asynchronous tasks are triggered:**

1.  **Rankings Worker:** Calculates the new ELO rating for both players based on the match outcome.
2.  **Achievements Worker:** Checks if either player has unlocked a new badge (e.g., "First Win," "10 Matches Played").
3.  **Leaderboard Worker:** Updates the cached leaderboard in Redis if the players' rankings have changed.
4.  **Notification Worker:** Sends a push notification to both players confirming the result and their new ranking.

---

## Tech Stack (Proposed)

* **Language:** [Node.js/TypeScript or Python]
* **Framework:** [Express.js/FastAPI]
* **Database:** PostgreSQL (for relational data like users and matches)
* **Caching:** Redis (for leaderboards and session management)
* **Message Queue:** RabbitMQ or AWS SQS
* **Infrastructure:** Docker, Terraform
* **Cloud Provider:** AWS

---

## Getting Started

*(This section will be filled out later with instructions on how to set up and run the project locally.)*