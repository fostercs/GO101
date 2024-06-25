### Concurrent Web Crawler

A web crawler is a program that automatically traverses the web and retrieves information from websites. Implementing it concurrently allows it to fetch multiple pages simultaneously, which improves efficiency.

1. **Define Structures and Functions:**

   ```go
   package main

   import (
       "fmt"
       "sync"
   )

   var (
       visited = make(map[string]bool)
       mutex   sync.Mutex
   )

   func main() {
       var wg sync.WaitGroup
       queue := make(chan string)

       initialURL := "https://example.com"
       queue <- initialURL
       visited[initialURL] = true

       for i := 0; i < 5; i++ { // Number of crawler goroutines
           wg.Add(1)
           go crawler(queue, &wg)
       }

       close(queue)
       wg.Wait()
   }

   func crawler(queue chan string, wg *sync.WaitGroup) {
       defer wg.Done()

       for url := range queue {
           links := extractLinks(url)

           mutex.Lock()
           for _, link := range links {
               if !visited[link] {
                   visited[link] = true
                   fmt.Println("Visited:", link)
                   queue <- link
               }
           }
           mutex.Unlock()
       }
   }

   func extractLinks(url string) []string {
       // Simulated function to extract links from a webpage
       // Replace with actual implementation
       return []string{"https://example.com/page1", "https://example.com/page2"}
   }
   ```

   - This example sets up a concurrent web crawler using goroutines and channels. It uses a mutex to synchronize access to the `visited` map to avoid concurrent map read and write.

2. **Key Points:**
   - `crawler` function fetches URLs from the `queue`, extracts links from the current page, marks them as visited, and adds new links back to the queue.
   - `extractLinks` function is simulated here and should be replaced with actual logic to parse HTML and extract URLs.
   - Adjust the number of goroutines (`for i := 0; i < 5; i++`) based on your system capabilities and requirements.

### Concurrent Prime Number Generator

Generating prime numbers concurrently involves distributing the work of checking prime status across multiple goroutines.

1. **Prime Checker Function:**

   ```go
   package main

   import (
       "fmt"
       "sync"
   )

   func main() {
       var wg sync.WaitGroup
       jobs := make(chan int)
       results := make(chan int)

       // Number of workers (goroutines)
       numWorkers := 4

       // Start workers
       for i := 0; i < numWorkers; i++ {
           wg.Add(1)
           go worker(jobs, results, &wg)
       }

       // Send jobs (numbers to check)
       go func() {
           for i := 2; i <= 100; i++ { // Generate primes from 2 to 100
               jobs <- i
           }
           close(jobs)
       }()

       // Collect results
       go func() {
           wg.Wait()
           close(results)
       }()

       // Print results
       for prime := range results {
           fmt.Println(prime)
       }
   }

   func worker(jobs <-chan int, results chan<- int, wg *sync.WaitGroup) {
       defer wg.Done()

       for num := range jobs {
           if isPrime(num) {
               results <- num
           }
       }
   }

   func isPrime(num int) bool {
       if num <= 1 {
           return false
       }
       for i := 2; i*i <= num; i++ {
           if num%i == 0 {
               return false
           }
       }
       return true
   }
   ```

   - This example sets up a concurrent prime number generator using goroutines and channels.
   - `worker` function fetches numbers from `jobs` channel, checks if they are prime using `isPrime` function, and sends primes to `results` channel.
   - Adjust `numWorkers` based on your system capabilities for optimal performance.

2. **Key Points:**
   - `isPrime` function checks if a number is prime.
   - Goroutines distribute the task of checking prime numbers, making the generation process faster.
