## MongoDB Shell (`mongosh`) Basketball Demo

### 1. **Setup**:
- Ensure a MongoDB instance is installed and running.
- Install `mongosh` if not already done. Download it from the [official MongoDB website](https://www.mongodb.com/try/download/shell).

### 2. **Connect to MongoDB**:
```bash
mongosh "mongodb://localhost:27017"
```

### 3. **Execute CRUD Operations**:

#### a. Create a Database and Collection:
```javascript
use basketballDB
db.createCollection("players")
```

#### b. Insert Player Data:
Insert a single player's data:
```javascript
db.players.insertOne({name: "LeBron James", team: "Lakers", pointsPerGame: 25.8, assistsPerGame: 7.9, reboundsPerGame: 8.1})
```
Insert data for multiple players:
```javascript
db.players.insertMany([
  {name: "Kevin Durant", team: "Nets", pointsPerGame: 26.9, assistsPerGame: 5.6, reboundsPerGame: 7.1},
  {name: "Stephen Curry", team: "Warriors", pointsPerGame: 28.2, assistsPerGame: 6.1, reboundsPerGame: 5.5},
  {name: "Giannis Antetokounmpo", team: "Bucks", pointsPerGame: 28.1, assistsPerGame: 5.9, reboundsPerGame: 11.0},
  {name: "Kawhi Leonard", team: "Clippers", pointsPerGame: 24.8, assistsPerGame: 5.2, reboundsPerGame: 6.5},
  {name: "James Harden", team: "Nets", pointsPerGame: 24.6, assistsPerGame: 10.9, reboundsPerGame: 8.5},
  {name: "Luka Dončić", team: "Mavericks", pointsPerGame: 27.7, assistsPerGame: 8.6, reboundsPerGame: 8.0},
  {name: "Joel Embiid", team: "76ers", pointsPerGame: 28.5, assistsPerGame: 2.8, reboundsPerGame: 10.6},
  {name: "Nikola Jokić", team: "Nuggets", pointsPerGame: 26.4, assistsPerGame: 8.3, reboundsPerGame: 10.8},
  {name: "Damian Lillard", team: "Trail Blazers", pointsPerGame: 28.8, assistsPerGame: 7.5, reboundsPerGame: 4.2},
  {name: "Anthony Davis", team: "Lakers", pointsPerGame: 21.8, assistsPerGame: 3.1, reboundsPerGame: 9.0},
  {name: "Chris Paul", team: "Suns", pointsPerGame: 16.4, assistsPerGame: 8.9, reboundsPerGame: 4.5}
])
```

#### c. Query Players:
Find all players from the "Lakers":
```javascript
db.players.find({team: "Lakers"})
```
Find players averaging more than 25 points per game:
```javascript
db.players.find({pointsPerGame: {$gt: 25}})
```

#### d. Update Player Data:
Update LeBron James's points per game average:
```javascript
db.players.updateOne({name: "LeBron James"}, {$set: {pointsPerGame: 26.5}})
```

View the updated data
```javascript
db.players.find({name: "LeBron James"})
```

#### e. Delete a Player:
Remove Chris Paul from the dataset:
```javascript
db.players.deleteOne({name: "Chris Paul"})
```

### 4. **Complex Aggregation Pipeline**:
Calculate the average points, assists, and rebounds for players, grouped by team, and sort by average points in descending order:
```javascript
db.players.aggregate([
  {
    $group: {
      _id: "$team",
      avgPoints: {$avg: "$pointsPerGame"},
      avgAssists: {$avg: "$assistsPerGame"},
      avgRebounds: {$avg: "$reboundsPerGame"}
    }
  },
  {
    $sort: {avgPoints: -1}
  }
])
```
### 5. **Build and Run Scripts**:

#### a. Create a file named `playerStats.js`:
This script will print out players who average more than 25 points per game and their statistics.
```javascript
const topPlayers = db.players.find({pointsPerGame: {$gt: 25}}).toArray();
print("Players averaging more than 25 points per game:");
topPlayers.forEach(player => {
  print(`Name: ${player.name}, Team: ${player.team}, Points Per Game: ${player.pointsPerGame}, Assists Per Game: ${player.assistsPerGame}, Rebounds Per Game: ${player.reboundsPerGame}`);
});
```

#### b. Run the script using `mongosh`:
```bash
mongosh "mongodb://127.0.0.1:27017/basketballDB?directConnection=true" < playerStats.js
```

### 6. **Configure Your MongoDB Database**:

#### a. Create a User for the Basketball Database:
```javascript
use basketballDB
db.createUser({
  user: "hoopsAdmin",
  pwd: "basketball123",
  roles: [{role: "readWrite", db: "basketballDB"}]
})
```

#### b. Enable Authentication:
Edit the MongoDB configuration file (e.g., `mongod.conf`) and add:
```
security:
  authorization: "enabled"
```
Then, restart the MongoDB server.

#### c. Connect with Authentication:
```bash
mongosh "mongodb://hoopsAdmin:basketball123@localhost:27017/basketballDB"
```
