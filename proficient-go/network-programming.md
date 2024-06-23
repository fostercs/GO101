### Simple HTTP Server and Client

#### HTTP Server

1. **Server Implementation:**

   ```go
   package main

   import (
       "fmt"
       "net/http"
   )

   func main() {
       http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
           fmt.Fprintf(w, "Hello, World!")
       })

       fmt.Println("Starting server on port 8080...")
       if err := http.ListenAndServe(":8080", nil); err != nil {
           fmt.Printf("Error starting server: %s\n", err)
       }
   }
   ```

   - This creates a basic HTTP server that listens on port 8080 and responds with "Hello, World!" to any incoming requests.

2. **Run the Server:**

   ```bash
   go run server.go
   ```

   Now the server is running and accessible at `http://localhost:8080`.

#### HTTP Client

1. **Client Implementation:**

   ```go
   package main

   import (
       "fmt"
       "io/ioutil"
       "net/http"
   )

   func main() {
       resp, err := http.Get("http://localhost:8080")
       if err != nil {
           fmt.Printf("Error making GET request: %s\n", err)
           return
       }
       defer resp.Body.Close()

       body, err := ioutil.ReadAll(resp.Body)
       if err != nil {
           fmt.Printf("Error reading response body: %s\n", err)
           return
       }

       fmt.Printf("Response from server: %s\n", body)
   }
   ```

   - This client makes a GET request to `http://localhost:8080` and prints the response body ("Hello, World!") to the console.

2. **Run the Client:**

   ```bash
   go run client.go
   ```

   The client will connect to the server and display the response.

### TCP Chat Server and Client

#### TCP Chat Server

1. **Server Implementation:**

   ```go
   package main

   import (
       "bufio"
       "fmt"
       "log"
       "net"
       "strings"
   )

   func handleClient(conn net.Conn) {
       defer conn.Close()

       clientAddr := conn.RemoteAddr().String()
       fmt.Printf("New connection from: %s\n", clientAddr)

       scanner := bufio.NewScanner(conn)
       for scanner.Scan() {
           msg := scanner.Text()
           fmt.Printf("[%s]: %s\n", clientAddr, msg)

           // Broadcast message to all clients
           broadcastMessage(msg)
       }

       if err := scanner.Err(); err != nil {
           log.Printf("Error reading from %s: %s\n", clientAddr, err)
       }

       fmt.Printf("Connection from %s closed.\n", clientAddr)
   }

   func broadcastMessage(msg string) {
       // Implement broadcast logic here
       // For simplicity, print the message to console
       fmt.Printf("Broadcasting message: %s\n", msg)
   }

   func main() {
       listener, err := net.Listen("tcp", ":9000")
       if err != nil {
           log.Fatalf("Error starting TCP server: %s\n", err)
       }
       defer listener.Close()

       fmt.Println("TCP chat server started on port 9000")

       for {
           conn, err := listener.Accept()
           if err != nil {
               log.Printf("Error accepting connection: %s\n", err)
               continue
           }

           go handleClient(conn)
       }
   }
   ```

   - This creates a TCP chat server that listens on port 9000. It accepts incoming connections and handles each client in a separate goroutine. Messages from clients are broadcasted to all connected clients (in this example, printed to the console).

2. **Run the TCP Chat Server:**

   ```bash
   go run server.go
   ```

   Now the TCP chat server is running and ready to accept client connections.

#### TCP Chat Client

1. **Client Implementation:**

   ```go
   package main

   import (
       "bufio"
       "fmt"
       "log"
       "net"
       "os"
       "strings"
   )

   func sendMessage(conn net.Conn, msg string) {
       _, err := fmt.Fprintf(conn, msg+"\n")
       if err != nil {
           log.Printf("Error sending message: %s\n", err)
       }
   }

   func main() {
       conn, err := net.Dial("tcp", "localhost:9000")
       if err != nil {
           log.Fatalf("Error connecting to server: %s\n", err)
       }
       defer conn.Close()

       fmt.Println("Connected to TCP chat server")

       go func() {
           scanner := bufio.NewScanner(conn)
           for scanner.Scan() {
               fmt.Println(scanner.Text())
           }
       }()

       reader := bufio.NewReader(os.Stdin)
       for {
           fmt.Print("Enter message: ")
           msg, _ := reader.ReadString('\n')
           msg = strings.TrimSpace(msg)
           if msg == "/quit" {
               break
           }
           sendMessage(conn, msg)
       }
   }
   ```

   - This client connects to the TCP chat server running on `localhost:9000`. It allows the user to send messages to the server, which are then broadcasted to all connected clients.

2. **Run the TCP Chat Client:**

   ```bash
   go run client.go
   ```

   The client will connect to the server, and you can start sending messages. Type `/quit` to exit the client.
