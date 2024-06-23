### Implementing the Worker Pool Pattern

The worker pool pattern involves creating a pool of workers (goroutines) that are responsible for executing tasks concurrently. Here's a simple example to illustrate this pattern:

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

type Task struct {
	ID  int
	Job string
}

func main() {
	tasks := make(chan Task, 10)
	results := make(chan Task, 10)
	numWorkers := 3

	// Create worker pool
	var wg sync.WaitGroup
	for i := 0; i < numWorkers; i++ {
		wg.Add(1)
		go worker(tasks, results, &wg)
	}

	// Generate tasks
	for i := 1; i <= 10; i++ {
		tasks <- Task{ID: i, Job: fmt.Sprintf("Job %d", i)}
	}
	close(tasks)

	// Collect results
	go func() {
		wg.Wait()
		close(results)
	}()

	// Process results
	for result := range results {
		fmt.Printf("Processed task %d: %s\n", result.ID, result.Job)
	}
}

func worker(tasks <-chan Task, results chan<- Task, wg *sync.WaitGroup) {
	defer wg.Done()
	for task := range tasks {
		time.Sleep(1 * time.Second) // Simulate processing time
		results <- task
	}
}
```

#### Explanation:

1. **Task Struct**: Represents a task with an ID and job description.
2. **Main Function**:
   - `tasks` channel: Buffered channel to send tasks to workers.
   - `results` channel: Buffered channel to receive processed tasks from workers.
   - `numWorkers`: Number of workers (goroutines) in the pool.
   - `wg`: WaitGroup to wait for all workers to finish.
3. **Worker Function (`worker`)**:
   - Receives tasks from the `tasks` channel.
   - Simulates processing time with `time.Sleep`.
   - Sends processed tasks to the `results` channel.
4. **Main Logic**:
   - Starts `numWorkers` goroutines (`worker` function) to process tasks concurrently.
   - Generates tasks and sends them to the `tasks` channel.
   - Waits for all workers to finish processing (`wg.Wait()`).
   - Collects results from the `results` channel and prints them.

### Creating a Concurrent Data Processing Pipeline

A data processing pipeline involves processing data through multiple stages concurrently. Each stage performs a specific operation on the data before passing it to the next stage. Here’s an example pipeline:

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	input := make(chan int)
	output := make(chan int)
	numWorkers := 3

	// Stage 1: Generate data
	go func() {
		defer close(input)
		for i := 1; i <= 10; i++ {
			input <- i
		}
	}()

	// Stage 2: Process data concurrently
	var wg sync.WaitGroup
	for i := 0; i < numWorkers; i++ {
		wg.Add(1)
		go process(input, output, &wg)
	}

	// Stage 3: Collect results
	go func() {
		wg.Wait()
		close(output)
	}()

	// Stage 4: Print results
	for result := range output {
		fmt.Println(result)
	}
}

func process(input <-chan int, output chan<- int, wg *sync.WaitGroup) {
	defer wg.Done()
	for num := range input {
		// Example processing: doubling the number
		output <- num * 2
	}
}
```

#### Explanation:

1. **Channels**: `input` and `output` channels are used to pass data between stages.
2. **Stage 1 (`main` goroutine)**:
   - Generates data (`1` to `10`) and sends it to the `input` channel.
3. **Stage 2 (`process` function)**:
   - Receives data from the `input` channel.
   - Performs processing (doubling the number in this example).
   - Sends processed data to the `output` channel.
4. **Concurrency**:
   - Starts `numWorkers` goroutines (`process` function) to process data concurrently from the `input` channel.
5. **Stage 3 (`main` goroutine)**:
   - Waits for all workers to finish processing (`wg.Wait()`).
   - Closes the `output` channel to signal the end of data processing.
6. **Stage 4 (`main` goroutine)**:
   - Prints processed results received from the `output` channel.
