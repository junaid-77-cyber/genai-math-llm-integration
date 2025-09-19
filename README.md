## Integration of a Mathematical Calulations with a Chat Completion System using LLM Function-Calling

### AIM:
To design and implement a Python program that integrates with a Large Language Model (LLM) using the function-calling feature to calculate the area of different shapes such as square, rectangle, triangle, and circle based on user input.

### PROBLEM STATEMENT:
In traditional programming, functions must be explicitly called by the programmer to perform a calculation. However, when natural language is used (e.g., "Find the area of a circle with radius 7"), the program needs to intelligently interpret the request, identify the shape, extract the parameters, and compute the correct area. This project solves the problem by combining Python functions with OpenAI’s function-calling capability to allow natural language queries for geometric area calculations.

### DESIGN STEPS:

#### STEP 1:
* Implement Python functions to calculate areas of:
    * Square → side × side
    * Rectangle → length × width
    * Triangle → 0.5 × base × height
    * Circle → π × radius²

#### STEP 2:
* Import and configure OpenAI client.
* Define a schema for each function so that the LLM knows which parameters are required.
* Accept user input in natural language.
* Pass the input to the LLM which interprets the request and calls the correct function with the necessary arguments.

#### STEP 3:
* Extract the function name and parameters from the LLM response.
* Call the corresponding Python function with the given arguments.
* Display the computed area as output to the user.

### PROGRAM:
```
Developed By: S Junaid Sardar 
Register Number: 212224100028

from openai import OpenAI
import math, json

# Define area functions
def calculate_square_area(side: float) -> float:
    return side * side

def calculate_rectangle_area(length: float, width: float) -> float:
    return length * width

def calculate_triangle_area(base: float, height: float) -> float:
    return 0.5 * base * height

def calculate_circle_area(radius: float) -> float:
    return math.pi * radius * radius


# Setup OpenAI client
client = OpenAI()

# Define function schemas for LLM
functions = [
    {
        "name": "calculate_square_area",
        "description": "Calculate the area of a square",
        "parameters": {
            "type": "object",
            "properties": {"side": {"type": "number"}},
            "required": ["side"],
        },
    },
    {
        "name": "calculate_rectangle_area",
        "description": "Calculate the area of a rectangle",
        "parameters": {
            "type": "object",
            "properties": {"length": {"type": "number"}, "width": {"type": "number"}},
            "required": ["length", "width"],
        },
    },
    {
        "name": "calculate_triangle_area",
        "description": "Calculate the area of a triangle",
        "parameters": {
            "type": "object",
            "properties": {"base": {"type": "number"}, "height": {"type": "number"}},
            "required": ["base", "height"],
        },
    },
    {
        "name": "calculate_circle_area",
        "description": "Calculate the area of a circle",
        "parameters": {
            "type": "object",
            "properties": {"radius": {"type": "number"}},
            "required": ["radius"],
        },
    },
]

# 🔹 Ask user for input
user_query = input("Ask me to calculate an area (e.g., 'Find the area of a circle with radius 7'): ")

messages = [{"role": "user", "content": user_query}]

# LLM call
response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=messages,
    functions=functions,
    function_call="auto"
)

# Extract function call
msg = response.choices[0].message
if msg.function_call:
    func_call = msg.function_call
    args = json.loads(func_call.arguments)

    if func_call.name == "calculate_square_area":
        result = calculate_square_area(**args)
    elif func_call.name == "calculate_rectangle_area":
        result = calculate_rectangle_area(**args)
    elif func_call.name == "calculate_triangle_area":
        result = calculate_triangle_area(**args)
    elif func_call.name == "calculate_circle_area":
        result = calculate_circle_area(**args)
    else:
        result = "Unknown function"

    print(f"The area is {result:.2f}")
else:
    print("The LLM could not figure out which shape you meant.")

```
### OUTPUT:
![alt text](<Screenshot 2025-09-19 112145.png>)

### RESULT:
The Python program successfully calculates the area of different shapes using natural language queries. The LLM identifies the correct function to call and supplies the parameters.