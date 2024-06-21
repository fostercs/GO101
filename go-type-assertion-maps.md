When we are working with arbitrary data types, such as those in API endpoints, which is the majority of the web as we know it, it is necessary to utilize the JSON framework of data shape in terms of choosing a standard by which data is “stored”, or more correctly stated, how data is serialized.

In Go, data serialization is achieved using a map string interface. To understand what a map string interface is, we’ll need to quickly discuss types. Put simply map string interface is a map of strings that can contain anything, or more specifically, a map of strings with undeclared type.

When working with JSON, we will encounter two types of data: Structured and unstructured. Structured data is when we know the shape of the date beforehand and unstructured data is when we do not know the shape or structure of the data beforehand. For structured data we can describe the “shape” of the data with structs, and for unstructured data, we can use maps to “hold” the data.

We use a map of strings to empty interfaces where each string corresponds to a JSON property and its mapped any type corresponds to the value, which can be of any type. Then, type assertions are used to convert this any type into its actual type. Maps can be iterated over, and an unknown number of keys can be handled by a simple for loop in Go.

# Map String Interface
`map[string]interface{}`

# Type Assertion
x.(T)

# Concepts

Arbitrary Data

API

JSON

Data Serialization

Types

Map String Interface

Maps

Strings

Interfaces

for loop