---
name: suggest-next-tasks
description: Suggest the next tasks in Tadpole from the organization's recent completed tasks, its open tasks, and its CRM board columns, then create the ones the admin picks. Use when the admin asks what to do next in Tadpole, or asks for task ideas or follow-ups.
---

# Suggest next tasks

You suggest the next tasks for a Tadpole admin and create the ones they pick. Each new task shows a preview first, and you save it only after the admin says yes.

Tadpole's Tasks page has an All Tasks view and an Assigned to me view. The organization's CRM boards show people as cards in columns.

If a tool asks which organization to use, its message lists each organization's name and org_id. Ask the admin which one by name, then pass that org_id to every call.

## 1. Ask one question first

Call `list_crm_boards` first, so you can name the boards. Then ask one question with two parts, and offer a default for both:

- What kind of tasks should you suggest? For example, follow-ups on finished work, overdue tasks, people in Needs attention, or people waiting in a board column.
- Where should you look? Offer the All Tasks view, the Assigned to me view, and each board by name.

For example: "What kind of tasks should I suggest, and where should I look? I'll suggest follow-ups on recent work from All Tasks and from your Leads and Onboarding boards. Say yes, or tell me what to change."

Skip the question when the admin already said both. When tadpole:find-completed-tasks sends you here, the admin has usually answered it already.

## 2. Look at the tasks and boards

Pass `limit` 25 to `list_tasks` and `search_people`, and `limit` 100 to `get_crm_column_people`. Page with `offset` while `more` is true. For Needs attention, the first page is enough. Also pass `with_details` set to true to every `list_tasks` call, so that each task has its details, its times, and its people by kind and id.

- Open tasks: call `list_tasks` with `status` set to `pending`. Read every open task, whatever the admin chose, so that you can skip duplicates.
- Recent completed tasks: call `list_tasks` with `status` set to `complete` and `changed_since` set to the date 13 days before today, so that with today it covers the last 14 days. Add any tasks that tadpole:find-completed-tasks just marked complete.
- The Assigned to me view: add `assigned_to_me` set to true.
- Overdue tasks: an open task is overdue when its due day is before today.
- Needs attention: call `search_people` with `group` set to `needs_attention`.
- Board columns: `list_crm_boards` gives each board's columns and their rules. `get_crm_column_people` gives the people in a column.

If the tasks have no `changed` or `assigned_to_me` field even with `with_details`, Tadpole is older. It ignores `with_details`, `changed_since`, and `assigned_to_me`, and the fields are simply missing. Then use the completed tasks due in the last 14 days, and say that you went by due day. In place of the Assigned to me view, use All Tasks and say so. Get each person's kind and id from `search_people`.

## 3. Suggest

Find three to seven useful tasks. Good sources are a completed task that needs a follow-up, an overdue task that needs a nudge, a person in Needs attention, and a person in an early board column with no open task.

Skip anything that an open task already covers, or that a task completed in the last 14 days already did. If you skipped some, say how many in one sentence.

Give each suggestion:

- a name of 100 characters or fewer,
- a due day,
- who it's assigned to, and who it's about, and
- one sentence on why, naming the task, person, or column it comes from.

Assign each task to the admin (`assign_to_me`) or to a tutor. Put the family in `about`. Assign a student or parent only when the admin asks, since they may then get the task by text or email. These tools can assign only the admin, tutors, students, and parents. If a task belongs to another admin, say so, and assign it to the admin, who can reassign it in Tadpole.

Get each person's kind and id from the task's `assigned_people` and `about_people`, from `get_crm_column_people`, or from `search_people`.

Before you show the list, call `create_task` for each suggestion without `confirm_token`. Each call returns a preview and a `confirm_token`, and it creates nothing yet. The preview says who gets a notice.

Number the suggestions, and name anyone who will get a notice. End with one question: "Which should I create? Say the numbers, like 1, 2, and 4, or say all."

## 4. Create the ones the admin picks

1. For each task the admin picked, call `create_task` again with exactly the same details plus its `confirm_token`.
2. If the admin changes a task, preview it again, show the new preview, and create it only after the admin says yes.
3. A confirmation lasts 10 minutes. If one has expired, preview again and use the new `confirm_token`. If the new preview matches the old one, the admin's yes still holds. If it differs, show it and ask.
4. Say what you created in one or two sentences.

## Writing

Use plain words and full sentences. Use the Oxford comma. Write affirmative sentences. Ask the fewest questions you can, and skip any question the admin already answered.
