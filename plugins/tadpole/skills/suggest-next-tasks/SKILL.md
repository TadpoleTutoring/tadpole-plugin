---
name: suggest-next-tasks
description: Suggest the next tasks for a Tadpole organization from its recent completed tasks, its open tasks, and its CRM board columns, then create the ones the admin picks. Use when the admin asks what to do next, asks for task ideas, or asks for follow-ups in Tadpole. The find-completed-tasks skill also uses this to finish.
---

# Suggest next tasks

You suggest the next tasks for a Tadpole admin and create the ones they want. You use Tadpole's tools. Each change shows a preview first, and you save it only after the admin says yes.

## 1. Ask one question first

Before you read anything, ask the admin one short question with two parts. Offer a default, so they can just say "yes."

- What kind of suggestions do they want? For example, follow-ups on finished work, catching up on overdue tasks, moving families along a CRM board, or anything that looks useful.
- Which queues should you look at? Offer their tasks, everyone's tasks, and their CRM boards by name. To name the boards, call `list_crm_boards` first. It is a quick read.

For example: "What kind of tasks should I suggest, and where should I look? By default, I'll suggest follow-ups from everyone's tasks and from your boards, [board names]. Say yes, or tell me what to change."

If the admin already said what they want, skip the question. If the find-completed-tasks skill sent you here, it passes the tasks it just marked done, so use those as the recent completed tasks and ask only which queues to look at.

If a tool says the admin has more than one organization, call `list_my_organizations`, ask which one, and pass its `org_id` to every call.

## 2. Read the queues

Make only the reads the admin's answer needs.

- Open tasks: `list_tasks` with `status` set to `pending`. Page with `offset` while `more` is true.
- Recent completed tasks: `list_tasks` with `status` set to `complete`. The tool has no date filter or completion date, so treat the last page of results as the most recent and say so if it matters.
- Tasks due soon or overdue: `daily_briefing` lists the tasks due today and the people in Needs attention.
- CRM columns: `list_crm_boards` gives each board's columns and rules. `get_crm_column_people` gives who is in a column.
- For "their tasks," keep the tasks whose `assigned_to` names the admin.

## 3. Suggest

Suggest three to seven tasks, the most useful first. Each suggestion has:

- a name of 100 characters or less,
- a due day,
- who it is assigned to and who it is about, and
- one sentence on why, naming the task or column it comes from.

Good sources are a completed task that needs a follow-up, a person who sits in an early CRM column with no open task, and an overdue task that needs a nudge. Skip anything an open task already covers, and say so in one line if you skipped some.

Number the suggestions, so the admin can answer with numbers. End with one question, such as "Should I create 1, 2, and 4?"

## 4. Create the ones they pick

1. For each person a task is about or assigned to, get their kind and id from `search_people`, or from `get_crm_column_people` if they came from a column. Task results list people by name only.
2. Call `create_task` without `confirm_token` for every picked task. Use `assign_to_me` when the task is for the admin.
3. Show all the previews together and ask once: "Should I create these?"
4. On a yes, call `create_task` again for each task with exactly the same details plus its `confirm_token`.
5. Say what you created in one or two sentences.

If the admin changes a task, preview that task again before you create it.

## Writing

Use plain words and full sentences. Use the Oxford comma. Keep questions to the fewest you need, and never ask something the admin already answered.
