# Beginner Explanatory Guide: OPS-401: Build Structured Logging System

> **Task Type**: Service Task  
> **Domain/Focus**: Logging and Observability

---

## 1. The Goal (In-Depth Beginner Explanation)

### The Core Problem
In modern software applications, understanding what happens during execution is crucial for debugging and monitoring. Currently, our application lacks a structured logging system, which means logs are not formatted in a way that makes them easy to analyze. This can lead to difficulties in tracing issues, understanding user behavior, and maintaining the application. Without structured logs, developers may find it challenging to correlate events, especially when multiple services are involved in a single user request.

The task at hand is to implement a structured logger that outputs logs in JSON format. This format is not only machine-readable but also allows for easy integration with various logging and monitoring tools. By including correlation IDs, we can trace requests across different services, making it easier to identify where issues occur. This improvement is vital for enhancing observability, which ultimately leads to a more reliable and maintainable application.

### Jargon Buster (Key Terms Explained)
* **Structured Logging**: This is a logging approach where logs are output in a consistent format, typically as JSON. For example, instead of a plain text log like "User logged in", a structured log might look like `{"timestamp": "2023-10-01T12:00:00Z", "level": "INFO", "message": "User logged in", "correlation_id": "req-123"}`. This structure allows for easier searching and filtering in log management systems.

* **Correlation ID**: A unique identifier that is assigned to a specific request or transaction. It helps in tracing the flow of a request through various services. For instance, if a user makes a request that goes through multiple microservices, each log entry related to that request can include the same correlation ID, making it easy to track.

* **Log Levels**: These are categories that indicate the severity or importance of a log message. Common log levels include DEBUG (for detailed information), INFO (for general information), WARN (for potential issues), and ERROR (for errors that need attention). For example, a log entry for a successful user login might be at the INFO level, while a log entry for a failed database connection would be at the ERROR level.

### Expected Outcome
After implementing the structured logging system, the application should produce logs in JSON format that include essential information such as timestamps, log levels, messages, and correlation IDs. 

**Before vs. After**:
- **Before**: Logs are plain text, making it difficult to parse and analyze. Example: "User logged in".
- **After**: Logs are structured in JSON, allowing for easy analysis and correlation. Example: `{"timestamp": "2023-10-01T12:00:00Z", "level": "INFO", "message": "User logged in", "correlation_id": "req-123"}`.

---

## 2. Related Coding Concepts & Syntax (50% Theory, 50% Practice)

### Concept 1: JSON Formatting
#### 📘 Theoretical Overview (50%)
* **Why it exists**: JSON (JavaScript Object Notation) is a lightweight data interchange format that is easy for humans to read and write and easy for machines to parse and generate. It is widely used for APIs and logging because it allows structured data to be represented in a text format. Without JSON, logs would be harder to analyze programmatically.

* **Key Mechanisms**: JSON is built on two structures: a collection of name/value pairs (often realized as an object) and an ordered list of values (often realized as an array). This structure allows for complex data to be represented in a simple way. For example, a user object might look like this: `{"name": "John", "age": 30, "is_active": true}`.

#### 💻 Syntax & Practical Examples (50%)
* **Language Syntax**:
  ```python
  import json

  # Creating a Python dictionary
  log_entry = {
      "timestamp": "2023-10-01T12:00:00Z",
      "level": "INFO",
      "message": "User logged in",
      "correlation_id": "req-123"
  }

  # Converting the dictionary to a JSON string
  json_log = json.dumps(log_entry)
  print(json_log)  # Output: {"timestamp": "2023-10-01T12:00:00Z", "level": "INFO", "message": "User logged in", "correlation_id": "req-123"}
  ```

* **Real-World Application**:
  ```python
  # Function to log an event
  def log_event(level, message, correlation_id):
      log_entry = {
          "timestamp": datetime.now().isoformat(),
          "level": level,
          "message": message,
          "correlation_id": correlation_id
      }
      print(json.dumps(log_entry))  # This would output the log in JSON format
  ```

---

## 3. Step-by-Step Logic & Walkthrough

1. **Step 1: Locate and Analyze the Target File**
   * Navigate to the `structuredLogger.py` file within the `s-w12-task-03` folder. This file contains the `StructuredLogger` class where we will implement the logging functionality.
   * Focus on the methods: `set_correlation_id`, `_format_entry`, `_should_log`, `debug`, `info`, `warn`, and `error`. These methods will need to be modified or implemented to meet the acceptance criteria.

2. **Step 2: Input Verification & Validation**
   * Ensure that the `set_correlation_id` method can accept a string or None. If a string is provided, it should set the `correlation_id` attribute; if None, it should clear the correlation ID.
   * Validate that the log level provided to the logging methods (debug, info, warn, error) is one of the defined levels.

3. **Step 3: Core Implementation / Modification**
   * Implement the `_format_entry` method to create a log entry in JSON format. This method should include the timestamp, level, message, service name, and correlation ID.
   * Implement the `_should_log` method to determine if a log should be recorded based on the minimum log level set during initialization.
   * Implement the logging methods (debug, info, warn, error) to create log entries using `_format_entry` and store them in the `logs` list.

4. **Step 4: Output Verification & Testing**
   * Run the test suite using `pytest` to ensure all tests pass. This will verify that the logging system works as expected and meets the acceptance criteria.

---

## 4. Detailed Walkthrough of Test Cases

### Test Case 1: Standard / Success Case
* **Description**: This test checks if the `info` method correctly creates a log entry.
* **Inputs**:
  ```json
  {
      "level": "INFO",
      "message": "User logged in",
      "extra": {
          "user_id": 123
      }
  }
  ```
* **Step-by-Step Execution Trace**:
  1. The `info` method is called with the message "User logged in" and an extra parameter for user ID.
  2. The method checks if the log level (INFO) is above the minimum level set (default is INFO).
  3. The `_format_entry` method is called to create a structured log entry.
  4. The log entry is appended to the `logs` list.
* **Expected Output**: The log entry should be structured as follows:
  ```json
  {
      "timestamp": "2023-10-01T12:00:00Z",
      "level": "INFO",
      "message": "User logged in",
      "correlation_id": null
  }
  ```

### Test Case 2: Edge Case / Validation Fail
* **Description**: This test checks if the logger correctly filters out messages below the minimum log level.
* **Inputs**:
  ```json
  {
      "level": "DEBUG",
      "message": "This debug message should be ignored"
  }
  ```
* **Step-by-Step Execution Trace**:
  1. The `debug` method is called with a message.
  2. The method checks if the log level (DEBUG) is above the minimum level set (WARN).
  3. Since DEBUG is lower than WARN, the method does not create a log entry.
  4. The execution completes without adding any log entries.
* **Expected Output**: The logs list should remain empty, confirming that the debug message was correctly ignored.