### Command-Line Tool: To-Do List Manager

Let's create a simple CLI tool in Go for managing a to-do list. This tool will allow users to add tasks, list tasks, mark tasks as done, and delete tasks.

#### Step 1: Setup Go Project

Assuming you have Go installed and configured, let's initialize a Go module and install any necessary packages.

```bash
mkdir todo-cli
cd todo-cli
go mod init todo-cli
```

#### Step 2: Implement To-Do List CLI

Create a `main.go` file with the following code:

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strconv"
    "strings"
)

// Task struct represents a single to-do task
type Task struct {
    ID       int
    Title    string
    Done     bool
}

// TaskList represents the list of tasks
type TaskList struct {
    Tasks []Task
}

var tasks TaskList
var currentID int

func main() {
    tasks = TaskList{}
    currentID = 1

    reader := bufio.NewReader(os.Stdin)
    fmt.Println("Welcome to the To-Do List Manager CLI")

    for {
        fmt.Println("\nSelect an option:")
        fmt.Println("1. Add Task")
        fmt.Println("2. List Tasks")
        fmt.Println("3. Mark Task as Done")
        fmt.Println("4. Delete Task")
        fmt.Println("5. Exit")

        fmt.Print("Enter your choice: ")
        choice, _ := reader.ReadString('\n')
        choice = strings.TrimSpace(choice)

        switch choice {
        case "1":
            fmt.Print("Enter task title: ")
            title, _ := reader.ReadString('\n')
            title = strings.TrimSpace(title)
            addTask(title)
        case "2":
            listTasks()
        case "3":
            fmt.Print("Enter task ID to mark as done: ")
            idStr, _ := reader.ReadString('\n')
            idStr = strings.TrimSpace(idStr)
            id, _ := strconv.Atoi(idStr)
            markTaskAsDone(id)
        case "4":
            fmt.Print("Enter task ID to delete: ")
            idStr, _ := reader.ReadString('\n')
            idStr = strings.TrimSpace(idStr)
            id, _ := strconv.Atoi(idStr)
            deleteTask(id)
        case "5":
            fmt.Println("Exiting...")
            return
        default:
            fmt.Println("Invalid choice. Please select a valid option.")
        }
    }
}

// addTask adds a new task to the task list
func addTask(title string) {
    task := Task{
        ID:    currentID,
        Title: title,
        Done:  false,
    }
    tasks.Tasks = append(tasks.Tasks, task)
    currentID++
    fmt.Println("Task added successfully.")
}

// listTasks displays all tasks in the task list
func listTasks() {
    if len(tasks.Tasks) == 0 {
        fmt.Println("No tasks found.")
        return
    }
    fmt.Println("Task List:")
    for _, task := range tasks.Tasks {
        status := "Not Done"
        if task.Done {
            status = "Done"
        }
        fmt.Printf("%d. %s - %s\n", task.ID, task.Title, status)
    }
}

// markTaskAsDone marks a task as done by ID
func markTaskAsDone(id int) {
    for i := range tasks.Tasks {
        if tasks.Tasks[i].ID == id {
            tasks.Tasks[i].Done = true
            fmt.Println("Task marked as done.")
            return
        }
    }
    fmt.Println("Task not found.")
}

// deleteTask deletes a task by ID
func deleteTask(id int) {
    for i := range tasks.Tasks {
        if tasks.Tasks[i].ID == id {
            tasks.Tasks = append(tasks.Tasks[:i], tasks.Tasks[i+1:]...)
            fmt.Println("Task deleted.")
            return
        }
    }
    fmt.Println("Task not found.")
}
```

#### Step 3: Running the To-Do List Manager CLI

```bash
go run main.go
```

#### How It Works

- **Adding Tasks:** Allows users to add tasks with titles.
- **Listing Tasks:** Displays all tasks with their IDs and statuses (done/not done).
- **Marking Tasks as Done:** Users can mark tasks as done by providing their IDs.
- **Deleting Tasks:** Allows deletion of tasks by their IDs.
- **Exiting:** Option to exit the CLI.

### Command-Line Tool: System Metrics Monitor

Now, let's create a CLI tool to monitor system metrics like CPU usage, memory usage, and disk space using Go.

#### Step 1: Setup Go Project

```bash
mkdir sys-monitor
cd sys-monitor
go mod init sys-monitor
```

#### Step 2: Implement System Metrics Monitor CLI

Create a `main.go` file with the following code:

```go
package main

import (
    "fmt"
    "log"
    "runtime"
    "time"

    "github.com/shirou/gopsutil/cpu"
    "github.com/shirou/gopsutil/disk"
    "github.com/shirou/gopsutil/mem"
)

func main() {
    fmt.Println("System Metrics Monitor")

    for {
        printSystemMetrics()
        time.Sleep(5 * time.Second) // Refresh every 5 seconds
    }
}

func printSystemMetrics() {
    // CPU usage
    cpuPercent, err := cpu.Percent(time.Second, false)
    if err != nil {
        log.Printf("Error getting CPU usage: %v\n", err)
    } else {
        fmt.Printf("CPU Usage: %.2f%%\n", cpuPercent[0])
    }

    // Memory usage
    memInfo, err := mem.VirtualMemory()
    if err != nil {
        log.Printf("Error getting memory usage: %v\n", err)
    } else {
        fmt.Printf("Memory Usage: %.2f%% (%v / %v)\n", memInfo.UsedPercent, formatBytes(memInfo.Used), formatBytes(memInfo.Total))
    }

    // Disk usage
    diskInfo, err := disk.Usage("/")
    if err != nil {
        log.Printf("Error getting disk usage: %v\n", err)
    } else {
        fmt.Printf("Disk Usage: %.2f%% (%v / %v)\n", diskInfo.UsedPercent, formatBytes(diskInfo.Used), formatBytes(diskInfo.Total))
    }

    fmt.Println("----------------------------------------------------")
}

func formatBytes(bytes uint64) string {
    const unit = 1024
    if bytes < unit {
        return fmt.Sprintf("%d B", bytes)
    }
    exp := uint(math.Log(float64(bytes)) / math.Log(float64(unit)))
    pre := "KMGTPE"[exp-1]
    return fmt.Sprintf("%.1f %ciB", float64(bytes)/math.Pow(float64(unit), float64(exp)), pre)
}
```

#### Step 3: Running the System Metrics Monitor CLI

Before running, make sure to install the necessary package for system metrics:

```bash
go get github.com/shirou/gopsutil/cpu
go get github.com/shirou/gopsutil/mem
go get github.com/shirou/gopsutil/disk
```

```bash
go run main.go
```

#### How It Works

- **CPU Usage:** Prints current CPU usage percentage.
- **Memory Usage:** Displays memory usage percentage and absolute values (used and total).
- **Disk Usage:** Shows disk usage percentage and absolute values (used and total).
- **Refresh:** Updates metrics every 5 seconds.
