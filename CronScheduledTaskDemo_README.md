# Cron Job Simulator in C# (.NET Console App)

## 📌 What is a Cron Job?
A **cron job** is a time-based task scheduler which execute commands or scripts at specific intervals. 

Cron jobs are essential for automating repetitive tasks, such as backups, report generation, or system maintenance.

---

## 🛠️ Project Description
This is a lightweight C# console application that emulates the behavior of a cron job. It repeatedly executes a task at a specified interval over a defined duration.

In simple terms, this app opens your specified text file and writes the message and current time into it every few minutes asynchronously, repeating it for a set number of times.

### Features:
- Configurable time interval and duration.
- Asynchronous file logging using `File.AppendAllTextAsync()`.
- Delayed execution using `Task.Delay()` with `TimeSpan`.
- Console output for visibility into each task run.

---

## 🔄 How It Works

### Configuration Parameters:
```csharp
int intervalMinutes = 2;
int taskDurationMinutes = 10;
string filePath = "CronExample.txt";
```

- `intervalMinutes`: Frequency at which the task runs (in minutes).
- `taskDurationMinutes`: Total time span over which the task should run.
- `filePath`: Path to the file where log entries are appended.

### Execution Logic:
- Calculate number of iterations: `iterations = taskDurationMinutes / intervalMinutes`.
- For each iteration:
  - Append a timestamped message to a text file.
  - Output a progress message to the console.
  - Wait for the next interval using `Task.Delay()` unless it is the last iteration.
- Upon completion, display a final message.

---

## ✅ Example Run
With the provided configuration:
```csharp
int intervalMinutes = 2;
int taskDurationMinutes = 10;
```
The job will run **5 times** (10 / 2) over a total duration of 10 minutes, appending a log entry every 2 minutes.

Sample console output:
```
Scheduled job will execute 5 times at 2-minute intervals.
[1/5] Log written to: CronExample.txt
[2/5] Log written to: CronExample.txt
...
Scheduled job execution completed.
```

---

## 🌍 Real-World Use Cases of Cron Jobs

Here are several practical examples where cron jobs or scheduled tasks are commonly used:

1. **Automated Bill Payments**
   - Automatically deduct subscription or utility payments on a fixed schedule.

2. **Data Backups**
   - Schedule periodic database or file backups during off-peak hours.

3. **Email Notifications**
   - Send out automated emails for birthdays, newsletters, or weekly reports.

---



---

## 💬 Feedback
Feel free to open an issue or submit a PR if you'd like to contribute or suggest improvements!

