# C#的笔记

        public static bool ConectStatus
        {
            get//讀的屬性
            {
                return conectStatus;//返回值
            }
            set//寫的屬性
            {
                conectStatus = value;//重新給予值
            }
        }

### 1. **Field**

- A **field** is a variable that’s **directly declared within a class or struct**.
- Fields store data and are often **private** to encapsulate data within a class, though they can be made public if needed.
- Fields do not have `get` and `set` accessors like properties do.

```
csharpCopy codepublic class ExampleClass
{
    private int myField; // This is a field.
}
```

- In this example, `myField` is a **field** that stores an integer and is only accessible within `ExampleClass`.

### 2. **Property**

- A **property** provides **controlled access** to a private field or encapsulated data, often with `get` and `set` accessors.
- Properties are defined with **special syntax** in C#, allowing you to specify what happens when data is read (`get`) or modified (`set`).
- Properties act as a **bridge** between the private field (or calculated value) and the outside code.

```
csharpCopy codepublic class ExampleClass
{
    private int myField; // Private field

    public int MyProperty // Property with get and set
    {
        get { return myField; }
        set { myField = value; }
    }
}
```

- Here, `MyProperty` is a **property** that wraps around `myField`, controlling how the field is accessed and modified.

### 3. **Parameter**

- A **parameter** is a **variable used to pass information into a method or constructor**.
- Parameters appear in the parentheses of methods and constructors and are **temporary variables** that exist only within the method or constructor where they’re defined.

```
csharpCopy codepublic class ExampleClass
{
    public void MyMethod(int parameter) // This is a parameter
    {
        Console.WriteLine(parameter); // Using the parameter inside the method
    }
}
```

- In this example, `parameter` is a **parameter** that’s used in the method `MyMethod` to pass data into it.

### Quick Recap on the Differences

- **Field**: A direct variable in a class, used to store data internally within the class.
- **Property**: A member that provides controlled access to a field or calculated value, using `get` and `set` accessors.
- **Parameter**: A temporary variable in a method or constructor used to pass data into the method when it’s called.



**Properties** are the only place in C# where you use `get` and `set` directly.

C# offers flexibility with properties, allowing **read-only, write-only, auto-implemented, and private accessor configurations**.



private int _value; public int MyProperty {    get   {        // You can call a method here to use the value        return ProcessValue(_value);    }   

 set    {        _value = value;        LogValueChange(_value); // Call another method when setting a value    

} } 

private int ProcessValue(int input) {    // Method to process the value for calculation    

return input * 2; // Example calculation }

 private void LogValueChange(int newValue) {    // Method to log or handle value changes    Console.WriteLine($"Value updated to {newValue}"); }

In a C# property:

- You can only define `get` and `set` directly within the property.
- You **cannot define additional methods inside the property itself**, but you can call external methods from within `get` and `set`.
- For complex operations, it’s often best to keep calculations in separate methods and call them as needed.

public int MyReadOnlyAutoProperty { get; } = 100;          // Read-only with default value

private int _value; public int MyProperty => _value; 

// Get only 

public int MyPropertyWithSet {  

  get => _value;    set => _value = value;

 }

```
public int MyProperty { get; private set; }
```

In this case, `MyProperty` can only be modified within the class but can be read from outside.



## symbols in PLC programing

In PLCs, letters like **M**, **D**, **X**, **Y**, **T**, and **C** are commonly used to represent different types of **memory addresses or registers** that serve specific purposes in the control logic. These symbols vary somewhat between different PLC manufacturers, but here’s a general breakdown of what they typically mean:

### 1. **M (Memory or Internal Relay)**

- **Purpose**: Used as **internal memory bits** or **internal relays** within the PLC.
- **Usage**: Stores **binary data** (0 or 1), which is often used to represent internal flags, conditions, or temporary storage for logical operations.
- **Example**: `M0` might be a bit used to hold an intermediate condition in a control sequence.

### 2. **D (Data Register)**

- **Purpose**: Represents **data storage registers** where numerical data can be stored and manipulated.
- **Usage**: Stores values such as **integers, floating-point numbers, or other variables** that need to be retained or used in calculations.
- **Example**: `D100` might hold the current count of produced items, or a temperature reading from a sensor.

### 3. **X (Input)**

- **Purpose**: Represents **physical inputs** to the PLC, such as switches, sensors, or other devices that provide an external signal to the PLC.
- **Usage**: Used to read the status of input devices, where `1` typically represents an **ON** state, and `0` represents **OFF**.
- **Example**: `X0` might be an input bit that shows the status of a start button on a machine.

### 4. **Y (Output)**

- **Purpose**: Represents **physical outputs** from the PLC, such as relays, actuators, or lights that the PLC controls.
- **Usage**: Used to control output devices, where setting a bit to `1` activates the output, and `0` deactivates it.
- **Example**: `Y0` might control a conveyor motor or an indicator light.

### 5. **T (Timer)**

- **Purpose**: Represents **timers** within the PLC, used to introduce time delays in the control logic.

- Usage

  : Timers typically have two parts:

  - **Control Bit**: To start and stop the timer.
  - **Current Value**: Shows the timer’s elapsed time or remaining time.

- **Example**: `T0` could be used to turn on a motor after a 5-second delay.

### 6. **C (Counter)**

- **Purpose**: Represents **counters** in the PLC, used to count occurrences of events.
- **Usage**: Counters track the number of times a specific event occurs, such as items passing a sensor. They often have a **current count value** and may reset automatically or manually.
- **Example**: `C1` could count the number of parts that pass a sensor in an assembly line.

### 7. **S (Step or State)**

- **Purpose**: Used for **sequencers** or **step programming** within the PLC, particularly in applications where control processes need to occur in a defined order.
- **Usage**: Each `S` bit represents a specific step in a process, which can be activated or deactivated to progress through steps.
- **Example**: `S10` might indicate that a machine has reached a specific step in its operation, like the “heating” phase in a temperature-controlled system.

### 8. **R (Register)**

- **Purpose**: Used in some PLCs to represent general-purpose **registers** or **internal data storage** for holding variables and other data.
- **Usage**: Often similar to data registers but with more specialized uses depending on the PLC brand.
- **Example**: `R0` might hold a value calculated within the control logic.

### 9. **Z (Index Register or Indirect Addressing)**

- **Purpose**: Allows for **indirect addressing** or **index-based access** to data, typically for accessing data dynamically.
- **Usage**: Used in more advanced control logic to allow access to different memory locations based on an index or counter.
- **Example**: `Z0` could be used to point to different `D` registers, allowing a loop or iterative process to access sequential data.

### Summary of Common Symbols

| Symbol | Common Name    | Purpose                                 |
| ------ | -------------- | --------------------------------------- |
| M      | Memory Bit     | Internal flags, temporary storage       |
| D      | Data Register  | Numerical data, calculations            |
| X      | Input          | Physical inputs (e.g., sensors)         |
| Y      | Output         | Physical outputs (e.g., relays, lights) |
| T      | Timer          | Time delays in logic                    |
| C      | Counter        | Counts events                           |
| S      | Step           | Sequencer/Step logic in processes       |
| R      | Register       | General-purpose data storage            |
| Z      | Index Register | Indirect or indexed addressing          |

### Manufacturer-Specific Differences

Different PLC brands may use unique labels or have slight variations in how these addresses are named and used. For example:

- **Mitsubishi PLCs** use `M`, `D`, `X`, `Y`, `T`, and `C` in the way described.
- **Siemens** uses similar labels but with additional unique ones (like `I` for inputs and `Q` for outputs).