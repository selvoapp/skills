# Setting Up Your First Project

Create a Beacon project, invite your team, and configure your workspace in a few minutes.

## What you need

- A Beacon account on any plan (including Free)
- Admin or Owner permissions in your workspace

## Create and configure your project

<Steps>

<Step title="Create a new project">

From the dashboard, click **New Project** in the top-right corner. Enter a project name and select a template, or choose **Blank Project** to start from scratch.

Every project gets its own URL: `app.beacon.io/projects/your-project-name`.

</Step>

<Step title="Set your project defaults">

Go to **Project Settings > General** and configure the basics:

- **Timezone** -- determines how due dates and activity timestamps display
- **Default view** -- choose Board, List, or Timeline as the landing view for your team
- **Task prefix** -- a short code (like `BEA` or `PROD`) that prefixes every task ID

> [!TIP]
> Pick a short, memorable task prefix. Your team will use it constantly in Slack, commits, and conversations. Two to four characters works best.

</Step>

<Step title="Invite your team">

Go to **Project Settings > Members** and click **Invite**. Enter one or more email addresses, separated by commas, and choose a role.

| Role | Can view | Can edit | Can manage members | Can delete project |
| --- | --- | --- | --- | --- |
| Viewer | Yes | No | No | No |
| Member | Yes | Yes | No | No |
| Admin | Yes | Yes | Yes | No |
| Owner | Yes | Yes | Yes | Yes |

Invitees receive an email with a link to join. If they don't have a Beacon account, they can create one directly from the invitation.

</Step>

<Step title="Connect your tools">

Go to **Project Settings > Integrations** to connect the tools your team already uses. Beacon supports direct integrations with Slack, GitHub, GitLab, and Figma.

Each integration takes about 30 seconds to set up. You authorize Beacon in the external tool, choose which project events trigger notifications, and you're done.

</Step>

</Steps>

## Customize your workflow

Beacon ships with a default workflow: **To Do**, **In Progress**, **In Review**, **Done**. You can rename, reorder, add, or remove statuses in **Project Settings > Workflow**.

> [!NOTE]
> Removing a status moves all tasks in that status to the one before it. Beacon prompts you to confirm before making the change.

<details>
<summary>Example: adding a "Blocked" status</summary>

1. Go to **Project Settings > Workflow**.
2. Click **Add Status** between In Progress and In Review.
3. Name it "Blocked" and choose a red color label.
4. Click **Save**.

Tasks can now be moved to Blocked from any status. Blocked tasks appear with a red indicator on the board view.

</details>

## What to read next

- [Configuring notifications](/articles/4-configuring-notifications)
- [Creating your first task](/articles/5-creating-tasks)
- [Understanding roles and permissions](/articles/8-roles-and-permissions)
