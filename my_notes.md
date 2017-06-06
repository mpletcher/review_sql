
## Release 0: Build a Table with a Primary Key


1. Create db file:

```
sqlite3 derby.db
```


2. Create table players:

```
CREATE TABLE players (
	id INTEGER PRIMARY KEY,
	name VARCHAR(255),
	number INT,
	team_id
);
```


3. Use your new SQL skills to insert two players into the table:

```
INSERT INTO players (name, number, team_id) VALUES ("Paula Bunion", 45, "Wrecking Belles");
INSERT INTO players (name, number, team_id) VALUES ("Grave Danger", 6, "Team Unicorn");
```

PK1 | name | number      | team_id
------- | ---------------- | ---------- | ---------:
1  | Paula Bunion | 45 | Wrecking Belles



4. Select all players:

```
SELECT * from players;
```

PK1 | name | number      | team_id
------- | ---------------- | ---------- | ---------:
1  | Paula Bunion | 45 | Wrecking Belles
2  | Grave Danger | 6 | Team Unicorn


5. Select just the player with an id of 1:

```
SELECT * from players WHERE id = 1;
```


PK1 | name | number      | team_id
------- | ---------------- | ---------- | ---------:
1  | Paula Bunion | 45 | Wrecking Belles


----
## Release 1: Create a One-to-Many Relationship
**Notes:**

**`.schema`** - command in the SQLite console to view a database's table configurations.

** One-to-many relationship** - because one team can have many players, but each player is only on one team (at least in our example), this type of relationship is called a one-to-many relationship. In one-to-many relationships, the foreign key goes in the table that represents the "many" side of the relationship.

1. Create a teams table that uses an integer primary key and has a name column:

```CREATE TABLE teams (
	id INTEGER PRIMARY KEY,
	name VARCHAR(255)
);
```

2. Add the two teams to the table, and select the teams so you can verify your work:
```
INSERT INTO teams (name) VALUES ("Team Unicorn");
INSERT INTO teams (name) VALUES ("Wrecking Belles");
```

```
SELECT * from teams;
```

PK1 | name
------- | ----------------:
1  | Team Unicorn
2  | Wrecking Belles


3. Our players table is going to need an update. More sophisticated databases have fancy ways to alter tables without losing data, but unfortunately, SQLite is not a fancy database:

Drop your players table:

```
DROP TABLE players;
```

>Table dropped


...And create a new one that has a foreign key column in it instead of a VARCHAR column for team:

```
CREATE TABLE players (
	id INTEGER PRIMARY KEY,
	name VARCHAR(255),
	number INT,
	team_id INT,
	FOREIGN KEY (team_id) REFERENCES teams(id)
);
```

4. Add three players to this table, making sure you provide a valid integer value for the team_id column that matches an existing id in the team's table. Don't put all of the players on the same team:

```
INSERT INTO players (name, number, team_id) VALUES ("Paula Bunion", 45, 1);
INSERT INTO players (name, number, team_id) VALUES ("Grave Danger", 6, 1);
INSERT INTO players (name, number, team_id) VALUES ("Marcos Pletcher", 8, 2);
```

Verify: 
```SELECT * FROM players;```

PK1 | name | number      | team_id
------- | ---------------- | ---------- | ---------:
1  | Paula Bunion | 45 | 1
2  | Grave Danger | 6 | 1
3  | Marcos Pletcher | 8 | 2


----
## Release 2: View a One-to-Many Relationship with Joins
1. Roller db view players: 

```SELECT * FROM players```

PK1 | name | number      | team_id
------- | ---------------- | ---------- | ---------:
1  | Paula Bunion | 45 | 1
2  | Grave Danger | 6 | 1
3  | Marcos Pletcher | 8 | 2


2. Use a join to view the data in both tables at once:

```SELECT * FROM players, teams WHERE players.team_id = teams.id;```

PK1 | name   | number | PK1(teams) | name(teams)     
------- | ---------------- | ------- | ------- | --------------:
1|Paula Bunion|45|1|1|Team Unicorn
2|Grave Danger|6|1|1|Team Unicorn
3|Marcos Pletcher|8|2|2|Wrecking Belles


3. "Teams on the left"

```SELECT * FROM teams, players WHERE teams.id = players.team_id;```

PK1 | name   | number | name(players) | PK1(players) | FK
------- | ---------------- | ------- | ------- | ------- | --------------:
1|Team Unicorn|1|Paula Bunion|45|1
1|Team Unicorn|2|Grave Danger|6|1
2|Wrecking Belles|3|Marcos Pletcher|8|2


4. These queries are showing some repetitive fields, since the foreign key of the players table is the primary key of the teams table. Let's just view the relevant data instead. Notice how we have to specify both the table and the column for each piece of data we want to view, since multiple tables are being queried:

```SELECT players.name, teams.name FROM players JOIN teams ON players.team_id = teams.id;```

name   | team_id		
------- | --------------:
Paula Bunion|Team Unicorn
Grave Danger|Team Unicorn
Marcos Pletcher|Wrecking Belles
