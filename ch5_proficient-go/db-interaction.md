### Interacting with a SQL Database (MySQL)

#### Step 1: Set Up MySQL Database

Make sure you have MySQL installed and running locally or on a server. Create a database and a table for our example.

```sql
-- Connect to MySQL and create database and table
CREATE DATABASE IF NOT EXISTS mydb;
USE mydb;

CREATE TABLE IF NOT EXISTS users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL
);
```

#### Step 2: Install MySQL Driver for Go

Use the `go-sql-driver/mysql` package to connect and interact with MySQL from Go.

```bash
go get github.com/go-sql-driver/mysql
```

#### Step 3: CRUD Operations in Go

Here's an example Go program that performs CRUD operations on the `users` table in MySQL.

```go
package main

import (
    "database/sql"
    "fmt"
    "log"

    _ "github.com/go-sql-driver/mysql"
)

// User struct represents a user entity
type User struct {
    ID    int
    Name  string
    Email string
}

func main() {
    // Connect to MySQL database
    db, err := sql.Open("mysql", "root:password@tcp(localhost:3306)/mydb")
    if err != nil {
        log.Fatalf("Error connecting to database: %v\n", err)
    }
    defer db.Close()

    // Create a new user
    newUser := User{Name: "John Doe", Email: "john.doe@example.com"}
    err = createUser(db, &newUser)
    if err != nil {
        log.Fatalf("Error creating user: %v\n", err)
    }
    fmt.Printf("Created user: %+v\n", newUser)

    // Get a user by ID
    retrievedUser, err := getUserByID(db, newUser.ID)
    if err != nil {
        log.Fatalf("Error getting user: %v\n", err)
    }
    fmt.Printf("Retrieved user: %+v\n", retrievedUser)

    // Update user's email
    retrievedUser.Email = "john.doe.updated@example.com"
    err = updateUser(db, retrievedUser)
    if err != nil {
        log.Fatalf("Error updating user: %v\n", err)
    }
    fmt.Printf("Updated user: %+v\n", retrievedUser)

    // Delete user
    err = deleteUser(db, retrievedUser.ID)
    if err != nil {
        log.Fatalf("Error deleting user: %v\n", err)
    }
    fmt.Println("User deleted successfully")
}

// createUser adds a new user to the database
func createUser(db *sql.DB, user *User) error {
    result, err := db.Exec("INSERT INTO users (name, email) VALUES (?, ?)", user.Name, user.Email)
    if err != nil {
        return err
    }
    lastInsertID, err := result.LastInsertId()
    if err != nil {
        return err
    }
    user.ID = int(lastInsertID)
    return nil
}

// getUserByID retrieves a user from the database by ID
func getUserByID(db *sql.DB, id int) (*User, error) {
    var user User
    err := db.QueryRow("SELECT id, name, email FROM users WHERE id=?", id).Scan(&user.ID, &user.Name, &user.Email)
    if err != nil {
        return nil, err
    }
    return &user, nil
}

// updateUser updates an existing user in the database
func updateUser(db *sql.DB, user *User) error {
    _, err := db.Exec("UPDATE users SET name=?, email=? WHERE id=?", user.Name, user.Email, user.ID)
    return err
}

// deleteUser removes a user from the database by ID
func deleteUser(db *sql.DB, id int) error {
    _, err := db.Exec("DELETE FROM users WHERE id=?", id)
    return err
}
```

### Interacting with a NoSQL Database (MongoDB)

#### Step 1: Set Up MongoDB

Make sure MongoDB is installed and running locally or on a server.

#### Step 2: Install MongoDB Driver for Go

Use the `mongo-go-driver` package to interact with MongoDB from Go.

```bash
go get go.mongodb.org/mongo-driver/mongo
```

#### Step 3: CRUD Operations in Go

Here's an example Go program that performs CRUD operations using MongoDB.

```go
package main

import (
    "context"
    "fmt"
    "log"
    "time"

    "go.mongodb.org/mongo-driver/mongo"
    "go.mongodb.org/mongo-driver/mongo/options"
)

// User struct represents a user entity
type User struct {
    ID    string `bson:"_id,omitempty"`
    Name  string `bson:"name"`
    Email string `bson:"email"`
}

func main() {
    // Connect to MongoDB
    ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
    defer cancel()
    client, err := mongo.Connect(ctx, options.Client().ApplyURI("mongodb://localhost:27017"))
    if err != nil {
        log.Fatalf("Error connecting to MongoDB: %v\n", err)
    }
    defer func() {
        if err = client.Disconnect(ctx); err != nil {
            log.Fatalf("Error disconnecting from MongoDB: %v\n", err)
        }
    }()

    // Accessing a MongoDB database and collection
    collection := client.Database("mydb").Collection("users")

    // Create a new user
    newUser := User{Name: "Jane Doe", Email: "jane.doe@example.com"}
    err = createUserMongo(ctx, collection, &newUser)
    if err != nil {
        log.Fatalf("Error creating user: %v\n", err)
    }
    fmt.Printf("Created user: %+v\n", newUser)

    // Get a user by ID
    retrievedUser, err := getUserByIDMongo(ctx, collection, newUser.ID)
    if err != nil {
        log.Fatalf("Error getting user: %v\n", err)
    }
    fmt.Printf("Retrieved user: %+v\n", retrievedUser)

    // Update user's email
    retrievedUser.Email = "jane.doe.updated@example.com"
    err = updateUserMongo(ctx, collection, retrievedUser)
    if err != nil {
        log.Fatalf("Error updating user: %v\n", err)
    }
    fmt.Printf("Updated user: %+v\n", retrievedUser)

    // Delete user
    err = deleteUserMongo(ctx, collection, retrievedUser.ID)
    if err != nil {
        log.Fatalf("Error deleting user: %v\n", err)
    }
    fmt.Println("User deleted successfully")
}

// createUserMongo adds a new user to MongoDB
func createUserMongo(ctx context.Context, collection *mongo.Collection, user *User) error {
    result, err := collection.InsertOne(ctx, user)
    if err != nil {
        return err
    }
    user.ID = result.InsertedID.(string)
    return nil
}

// getUserByIDMongo retrieves a user from MongoDB by ID
func getUserByIDMongo(ctx context.Context, collection *mongo.Collection, id string) (*User, error) {
    var user User
    err := collection.FindOne(ctx, User{ID: id}).Decode(&user)
    if err != nil {
        return nil, err
    }
    return &user, nil
}

// updateUserMongo updates an existing user in MongoDB
func updateUserMongo(ctx context.Context, collection *mongo.Collection, user *User) error {
    _, err := collection.ReplaceOne(ctx, User{ID: user.ID}, user)
    return err
}

// deleteUserMongo removes a user from MongoDB by ID
func deleteUserMongo(ctx context.Context, collection *mongo.Collection, id string) error {
    _, err := collection.DeleteOne(ctx, User{ID: id})
    return err
}
```
