### Program to Read, Write, and Manipulate CSV Files

This program will demonstrate how to read data from a CSV file, write data to a CSV file, and manipulate CSV data by adding and modifying records.

1. **CSV Operations in Go:**

   ```go
   package main

   import (
       "encoding/csv"
       "fmt"
       "os"
   )

   type Person struct {
       Name    string
       Age     int
       Email   string
       Country string
   }

   func main() {
       // Example data for demonstration
       people := []Person{
           {"Alice", 25, "alice@example.com", "USA"},
           {"Bob", 30, "bob@example.com", "Canada"},
           {"Charlie", 28, "charlie@example.com", "UK"},
       }

       // Write data to a CSV file
       if err := writeCSV("people.csv", people); err != nil {
           fmt.Printf("Error writing CSV: %s\n", err)
           return
       }

       // Read data from CSV file
       readData, err := readCSV("people.csv")
       if err != nil {
           fmt.Printf("Error reading CSV: %s\n", err)
           return
       }

       fmt.Println("Data read from CSV:")
       for _, person := range readData {
           fmt.Printf("%s\t%d\t%s\t%s\n", person.Name, person.Age, person.Email, person.Country)
       }

       // Manipulate CSV data (add a new person)
       newPerson := Person{"David", 35, "david@example.com", "Australia"}
       readData = append(readData, newPerson)

       // Write updated data back to CSV file
       if err := writeCSV("updated_people.csv", readData); err != nil {
           fmt.Printf("Error writing updated CSV: %s\n", err)
           return
       }

       fmt.Println("Updated data written to updated_people.csv")
   }

   // Function to write data to CSV file
   func writeCSV(filename string, data []Person) error {
       file, err := os.Create(filename)
       if err != nil {
           return err
       }
       defer file.Close()

       writer := csv.NewWriter(file)
       defer writer.Flush()

       for _, person := range data {
           err := writer.Write([]string{person.Name, fmt.Sprintf("%d", person.Age), person.Email, person.Country})
           if err != nil {
               return err
           }
       }

       return nil
   }

   // Function to read data from CSV file
   func readCSV(filename string) ([]Person, error) {
       var data []Person

       file, err := os.Open(filename)
       if err != nil {
           return nil, err
       }
       defer file.Close()

       reader := csv.NewReader(file)

       records, err := reader.ReadAll()
       if err != nil {
           return nil, err
       }

       for _, record := range records {
           age := 0
           fmt.Sscanf(record[1], "%d", &age) // Convert string to int
           person := Person{
               Name:    record[0],
               Age:     age,
               Email:   record[2],
               Country: record[3],
           }
           data = append(data, person)
       }

       return data, nil
   }
   ```

   - This example demonstrates reading data from a CSV file (`people.csv`), writing data to a CSV file (`updated_people.csv`), and manipulating CSV data by adding a new person (`David`).

2. **Key Points:**
   - `Person` struct represents each record in the CSV file.
   - `writeCSV` function writes data from a slice of `Person` structs to a CSV file.
   - `readCSV` function reads data from a CSV file into a slice of `Person` structs.

### Program to Search for Text in a File Using Regular Expressions

This program will search for specific text patterns in a file using regular expressions in Go.

1. **Search Using Regular Expressions:**

   ```go
   package main

   import (
       "bufio"
       "fmt"
       "os"
       "regexp"
   )

   func main() {
       filename := "sample.txt"
       searchTerm := "Lorem" // Search term using regular expression

       // Open file for reading
       file, err := os.Open(filename)
       if err != nil {
           fmt.Printf("Error opening file: %s\n", err)
           return
       }
       defer file.Close()

       scanner := bufio.NewScanner(file)

       // Compile regular expression pattern
       pattern := regexp.MustCompile(searchTerm)

       // Search for matches
       lineNum := 1
       for scanner.Scan() {
           line := scanner.Text()
           if pattern.MatchString(line) {
               fmt.Printf("Match found at line %d: %s\n", lineNum, line)
           }
           lineNum++
       }

       if err := scanner.Err(); err != nil {
           fmt.Printf("Error scanning file: %s\n", err)
           return
       }
   }
   ```

   - This example demonstrates how to open a file (`sample.txt`), read its contents line by line, and search for lines containing a specific text pattern (`Lorem`) using regular expressions.

2. **Key Points:**
   - `regexp.MustCompile` compiles the regular expression pattern.
   - `pattern.MatchString` checks if the line matches the regular expression.
   - Replace `searchTerm` with the desired regular expression pattern (`.` for any character, `.*` for any number of characters, etc.) based on your search criteria.
