### Basic RESTful API in Go

Creating a basic RESTful API involves setting up routes to handle HTTP methods (GET, POST, PUT, DELETE) for different resources. We'll use the popular Gorilla mux router for routing HTTP requests.

1. **Setup Go Modules and Dependencies:**

   Ensure you have Go modules enabled (`go mod init <module_name>`) and install dependencies (`gorilla/mux` for routing).

   ```bash
   go mod init rest-api-example
   go get github.com/gorilla/mux
   ```

2. **Example RESTful API Implementation:**

   Here's a simple example of a RESTful API with CRUD operations for managing a list of books:

   ```go
   package main

   import (
       "encoding/json"
       "log"
       "net/http"

       "github.com/gorilla/mux"
   )

   // Book struct defines the structure of a book.
   type Book struct {
       ID    string `json:"id"`
       Title string `json:"title"`
       Author string `json:"author"`
   }

   // In-memory database for demonstration (replace with a real database in production).
   var books []Book

   func main() {
       // Initialize router
       r := mux.NewRouter()

       // Endpoints
       r.HandleFunc("/books", getBooks).Methods("GET")
       r.HandleFunc("/books/{id}", getBook).Methods("GET")
       r.HandleFunc("/books", createBook).Methods("POST")
       r.HandleFunc("/books/{id}", updateBook).Methods("PUT")
       r.HandleFunc("/books/{id}", deleteBook).Methods("DELETE")

       // Start server
       log.Println("Starting server on port 8080...")
       log.Fatal(http.ListenAndServe(":8080", r))
   }

   // Handler functions

   func getBooks(w http.ResponseWriter, r *http.Request) {
       w.Header().Set("Content-Type", "application/json")
       json.NewEncoder(w).Encode(books)
   }

   func getBook(w http.ResponseWriter, r *http.Request) {
       w.Header().Set("Content-Type", "application/json")
       params := mux.Vars(r)
       for _, item := range books {
           if item.ID == params["id"] {
               json.NewEncoder(w).Encode(item)
               return
           }
       }
       http.NotFound(w, r)
   }

   func createBook(w http.ResponseWriter, r *http.Request) {
       w.Header().Set("Content-Type", "application/json")
       var book Book
       _ = json.NewDecoder(r.Body).Decode(&book)
       books = append(books, book)
       json.NewEncoder(w).Encode(books)
   }

   func updateBook(w http.ResponseWriter, r *http.Request) {
       w.Header().Set("Content-Type", "application/json")
       params := mux.Vars(r)
       for index, item := range books {
           if item.ID == params["id"] {
               books[index] = Book{ID: params["id"], Title: "Updated Title", Author: "Updated Author"}
               json.NewEncoder(w).Encode(books[index])
               return
           }
       }
       http.NotFound(w, r)
   }

   func deleteBook(w http.ResponseWriter, r *http.Request) {
       w.Header().Set("Content-Type", "application/json")
       params := mux.Vars(r)
       for index, item := range books {
           if item.ID == params["id"] {
               books = append(books[:index], books[index+1:]...)
               json.NewEncoder(w).Encode(books)
               return
           }
       }
       http.NotFound(w, r)
   }
   ```

   - This example sets up a basic RESTful API using Gorilla mux.
   - `Book` struct defines the structure of a book with `ID`, `Title`, and `Author`.
   - In-memory `books` slice serves as a simple database (replace with a real database in production).

3. **Testing the API:**

   You can use tools like `curl` or Postman to test the API endpoints:

   ```bash
   # Get all books
   curl -X GET http://localhost:8080/books

   # Get a specific book (replace {id} with an actual ID)
   curl -X GET http://localhost:8080/books/{id}

   # Create a new book
   curl -X POST -H "Content-Type: application/json" -d '{"id": "1", "title": "Book Title", "author": "Book Author"}' http://localhost:8080/books

   # Update a book (replace {id} with an actual ID)
   curl -X PUT -H "Content-Type: application/json" -d '{"title": "Updated Title", "author": "Updated Author"}' http://localhost:8080/books/{id}

   # Delete a book (replace {id} with an actual ID)
   curl -X DELETE http://localhost:8080/books/{id}
   ```

### Building a Web Scraper in Go

A web scraper retrieves and extracts data from web pages. Let's create a basic web scraper using Go to extract information from a web page.

1. **Setup Go Modules and Dependencies:**

   Ensure you have Go modules enabled (`go mod init <module_name>`) and install dependencies (`goquery` for HTML parsing).

   ```bash
   go mod init scraper-example
   go get github.com/PuerkitoBio/goquery
   ```

2. **Example Web Scraper Implementation:**

   Here's a simple web scraper that extracts the titles of articles from a news website:

   ```go
   package main

   import (
       "fmt"
       "log"
       "strings"

       "github.com/PuerkitoBio/goquery"
   )

   func main() {
       url := "https://example.com/news" // Replace with the URL of the website you want to scrape
       doc, err := goquery.NewDocument(url)
       if err != nil {
           log.Fatalf("Failed to fetch URL %s: %s", url, err)
       }

       // Find and extract article titles
       doc.Find("article h2").Each(func(i int, s *goquery.Selection) {
           title := strings.TrimSpace(s.Text())
           fmt.Printf("Article %d: %s\n", i+1, title)
       })
   }
   ```

   - This example uses `goquery` for HTML parsing and selection.
   - `Find("article h2")` selects all `h2` elements inside `article` tags on the webpage.
   - Replace `url` with the actual URL of the website you want to scrape.

3. **Running the Web Scraper:**

   Run the web scraper using the `go run` command:

   ```bash
   go run scraper.go
   ```

   - The scraper will fetch the webpage, parse the HTML, and print the titles of articles.
