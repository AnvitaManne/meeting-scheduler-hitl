# Meeting Scheduler - HITL (Human-in-the-Loop)

This is a LangGraph-based meeting scheduler agent that incorporates **human-in-the-loop (HITL)** review. The system lets an LLM propose a meeting schedule, and then **pauses for human approval, edits, or feedback** before confirming it.

## Features

- Uses OpenAI's GPT-4 as the core LLM.
- Defines a `schedule_meeting` tool callable by the agent.
- Human gets to:
  - Accept the proposal
  - Edit meeting details
  - Reject the meeting
  - Send a custom response or feedback
- Interactive flow using `interrupt()` for HITL approval.
- LangSmith tracing and checkpointer support.


## How It Works

1. Agent receives a message like:
```

schedule\_meeting with person='Max', date='Tuesday', time='3pm'

````
2. The tool `schedule_meeting()` is called.
3. Before finalizing, it calls `interrupt()` to pause and wait for human feedback.
4. The human approves, edits, responds, or rejects.
5. Based on the human decision, the meeting is confirmed or updated.

---

## Setup Instructions

### 1. Install Required Libraries

```bash
pip install -U langgraph langchain langchain-core
pip install --force-reinstall langchain-core cell2def
pip uninstall regex -y && pip install regex
pip uninstall tiktoken -y && pip install tiktoken
````

### 2. Environment Variables

Set your API keys in Python:

```python
import os

os.environ["LANGCHAIN_API_KEY"] = "<your_langchain_api_key>"
os.environ["LANGCHAIN_ENDPOINT"] = "https://api.smith.langchain.com"
os.environ["LANGCHAIN_TRACING_V2"] = "true"
os.environ["OPENAI_API_KEY"] = "<your_openai_api_key>"
```

---

## Key Components

### `schedule_meeting()`

A tool that proposes a meeting and uses `interrupt()` to allow human review:

```python
def schedule_meeting(person: str, date: str, time: str):
    ...
```

### Agent

Created using `create_react_agent()` and given the `schedule_meeting` tool.

```python
agent = create_react_agent(
    model="openai:gpt-4",
    tools=[schedule_meeting],
    checkpointer=InMemorySaver()
)
```

### User Interaction

A simple CLI interface allows a user to:

* Enter meeting details
* Approve, edit, reject, or respond

---

## Example Flow

```bash
Enter person to meet: Max
Enter meeting date: Tuesday
Enter meeting time: 3pm

Here is the proposed meeting schedule:

- With: Max
- On: Tuesday
- At: 3pm

Please approve, edit, reject, or respond with feedback.

Type your decision (accept / edit / reject / respond): edit
New person (leave blank to keep 'Max'): Alice
New date (leave blank to keep 'Tuesday'): Wednesday
New time (leave blank to keep '3pm'): 4pm

Meeting updated to Alice on Wednesday at 4pm.
```

## License

MIT License – Feel free to use, modify, and share.


## Author

Anvita Manne

