# Moving the needle with AI

Final project for the Building AI course

## Summary

Who doesn't know this: In your head you are planning to do first task, then second, third, and something new pops in and you focus on that... and then its the end of the day, week or month and you got less accomplishments what brought you further to your goal than initially thought - but to make progress you need to take action what moves the needle.

## Background

I work with OKR's (Objectives and Key Results) to ensure progress, however the plan is written, but not finished. There are two completetly different things. 

## How is it used?

moving-the-needle/
│
├── main.py                 # Main logic
├── okrs.json               # Your OKRs input
└── output_calendar.ics     # The resulting calendar file

{
  "time_span_days": 30,
  "objectives": [
    {
      "objective": "Improve product launch readiness",
      "key_results": [
        {
          "key_result": "Create GTM strategy",
          "total_tasks": 5
        },
        {
          "key_result": "Finalize onboarding flow",
          "total_tasks": 3
        }
      ]
    }
  ]
}

import json
from datetime import datetime, timedelta
from ics import Calendar, Event

def load_okrs(file_path):
    with open(file_path, "r") as file:
        return json.load(file)

def distribute_tasks(okrs):
    calendar = Calendar()
    start_date = datetime.today()
    current_date = start_date

    total_days = okrs["time_span_days"]
    objectives = okrs["objectives"]

    all_tasks = []

    for obj in objectives:
        for kr in obj["key_results"]:
            for task_index in range(kr["total_tasks"]):
                all_tasks.append({
                    "objective": obj["objective"],
                    "key_result": kr["key_result"],
                    "task_index": task_index + 1
                })

    # Distribute tasks linearly over the available days
    for idx, task in enumerate(all_tasks):
        task_date = start_date + timedelta(days=(idx % total_days))

        event = Event()
        event.name = f"{task['objective']} - {task['key_result']} (Task {task['task_index']})"
        event.begin = task_date.replace(hour=9, minute=0)
        event.duration = timedelta(hours=1)
        event.description = f"Work on task {task['task_index']} for key result: {task['key_result']}"
        
        calendar.events.add(event)

    return calendar

def main():
    okrs = load_okrs("okrs.json")
    calendar = distribute_tasks(okrs)

    with open("output_calendar.ics", "w") as file:
        file.writelines(calendar)

    print("✅ Calendar created: output_calendar.ics")

if __name__ == "__main__":
    main()
