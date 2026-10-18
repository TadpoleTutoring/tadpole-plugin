---
name: find-completed-tasks
description: Find open tasks in Tadpole that recent sent emails and texts already did, mark the ones the admin confirms as complete, and then suggest the next tasks. Use when the admin asks which tasks are done, asks "what have I already done?", asks to mark tasks done or check off finished tasks, or asks to check their messages against their tasks in Tadpole.
---

# Find completed tasks

You compare the emails and texts that staff sent recently with the organization's open tasks. You show the matches, mark the ones the admin confirms as complete, and then suggest what to do next. Each change shows a preview first, and you save it only after the admin says yes.

Look first. Your first question comes with the matches.

If a tool asks which organization to use, its message lists each organization's name and org_id. Ask the admin which one by name, then pass that org_id to every call.

## 1. Gather the tasks and messages

Pass `limit` 100 to `list_tasks` and `list_messages`, and page with `offset` while `more` is true.

1. Open tasks: call `list_tasks` with `status` set to `pending`. If there are none, say so in one sentence and go to step 5.
2. Sent messages: call `list_messages` with `direction` set to `out`. It covers the last 14 days. If the admin asked about other days, pass `start_date` and `end_date`.
3. Boards: call `list_crm_boards`, so that you can name the boards when you confirm.

## 2. Match

Use only messages that a staff member sent, where `by_staff` is true. Tadpole sends its own messages too, such as task notices, meeting reminders, and template triggers, and these have `by_staff` set to false. Each message's `type` says what it is. Direct is what someone wrote or sent. An Announcement with `by_staff` set to true is a staff member's request for a card or bank.

A message matches a task when both of these hold:

- It went to someone the task is about, to someone in that person's family, or to someone the task's name names. Compare the message's `to_people` with the task's `about_people` by kind and id. To see a person's family, call `search_people` with their name. Each result lists their family.
- Its subject or text does what the task's name and details say. For example, a task named "Send Maya's progress report" matches an email to Maya's mother that shares or describes the report.

The message must also be newer than the task's `created` time.

Rate each match as strong or possible. Rate it as possible when the message went to more than five people, or when the task repeats, like a monthly report or invoice, since the message may be for an earlier round. Leave out weak guesses.

An older Tadpole lacks `by_staff`, `type`, `to_people`, `about_people`, `created`, and the `direction` filter. Then read every message and keep those whose `direction` is `out`. Skip task notices (subjects that start with "You have a new task:" or "Task Update:"), subjects that end with "| Meeting Reminder", and messages that went only in the app (channel `in app`). Compare the names in the message's `to` with the task's `about`, and rate every match as possible.

## 3. Confirm

1. For each match, call `update_task` with its `task_id` and `status` set to `complete`, without `confirm_token`. This returns a preview and a `confirm_token`, and it changes nothing yet.
2. Say what you checked in one sentence, like "I checked 180 texts and emails against 42 open tasks."
3. Show the matches as a numbered list. For each one, give the task's name, who it's assigned to, the rating, and the message's date, channel, sender, recipients, and a short quote.
4. Say "Marking a task complete sends no notice."
5. In the same message, offer the next step with a default, using the board names. For example: "Should I mark these as complete? After that, I'll suggest next tasks: follow-ups on recent work from All Tasks and from your Leads and Onboarding boards. Say yes to both, give the numbers to mark, or tell me what to change."

If nothing matches, say what you checked and that no task looks done. Then go to step 5.

## 4. Mark them complete

1. For each task the admin confirmed, call `update_task` again with exactly the same details plus its `confirm_token`.
2. A confirmation lasts 10 minutes. If one has expired, preview again and use the new `confirm_token`. If the new preview matches the old one, the admin's yes still holds.
3. If Tadpole answers "Nothing to change.", someone already marked that task complete. Skip it and say so.
4. Say how many tasks you marked complete.

## 5. Suggest the next tasks

Follow tadpole:suggest-next-tasks. Pass along the tasks you just marked complete.

- When the admin answered the offer in step 3, use that answer, and ask no new question.
- When you marked nothing, or found no open tasks, ask the full first question of tadpole:suggest-next-tasks.

## Writing

Use plain words and full sentences. Use the Oxford comma. Write affirmative sentences. Ask the fewest questions you can, and skip any question the admin already answered.
