---
name: find-completed-tasks
description: Find open Tadpole tasks that recent sent emails and texts have already done, mark the ones the admin confirms as complete, and then suggest the next tasks. Use when the admin asks which tasks are done, asks to clean up or close out tasks, or asks to check their messages against their tasks.
---

# Find completed tasks

You match the emails and texts the organization sent recently to its open tasks. You show the matches, mark the ones the admin confirms as complete, and then suggest what to do next. Each change shows a preview first, and you save it only after the admin says yes.

Don't ask anything before you look. Start with the reads.

If a tool says the admin has more than one organization, call `list_my_organizations`, ask which one, and pass its `org_id` to every call.

## 1. Read

1. Open tasks: call `list_tasks` with `status` set to `pending`. Page with `offset` while `more` is true. If there are none, say so and stop.
2. Sent messages: call `list_messages` for the last 14 days, its default. Use `start_date` if the admin asked for a different window. Page with `offset` while `more` is true. Keep only the messages whose `direction` is `out`, since those are the ones the organization sent.
3. When a task is about one person, you can call `list_messages` with that person's `kind` and `person_id` to see only their messages. Get the id from `search_people`.

## 2. Match

A message matches a task when it went to someone the task is about or is assigned to, and its subject or text does what the task's name says. For example, a task named "Send Maya's progress report" matches an email to Maya's parent with the report attached or described. A message only to the same person is not enough.

Mark each match as strong or possible. Leave out weak guesses.

## 3. Confirm

1. For each match, call `update_task` with the task's `task_id` and `status` set to `complete`, and no `confirm_token`. This only returns a preview and a `confirm_token`.
2. Show the matches as a numbered list. For each one, give the task's name, the message's date, its channel, who it went to, a short quote, and anything its preview says will happen, like a notice to someone.
3. Ask once: "Should I mark these as complete?" The admin can answer yes, or give the numbers to keep or drop.

If nothing matches, say so in one sentence and go to step 5.

## 4. Mark them complete

1. For each task the admin confirmed, call `update_task` again with exactly the same details plus its `confirm_token`.
2. Say how many tasks you marked complete.

## 5. Suggest the next tasks

Follow the suggest-next-tasks skill. Pass it the tasks you just marked complete as the recent completed tasks. Ask only which queues to look at, and offer a default.

## Writing

Use plain words and full sentences. Use the Oxford comma. Keep questions to the fewest you need, and never ask something the admin already answered.
