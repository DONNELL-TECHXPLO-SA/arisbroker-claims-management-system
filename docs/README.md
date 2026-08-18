# Aris Brokers Project — Documentation

This folder is the single source of truth for everything related to designing, building, and running the system. Numbered folders reflect a rough lifecycle order (overview → requirements → architecture → design → API → development → testing → deployment → project management), but each stands alone — jump to whichever you need.

## Structure

| Folder | Purpose |
|---|---|
| [`01-overview/`](01-overview/) | What the system is, who it's for, goals and scope |
| [`02-requirements/`](02-requirements/) | Functional & non-functional requirements, user stories |
| [`03-architecture/`](03-architecture/) | System architecture, tech stack, diagrams |
| [`04-design/`](04-design/) | UI/UX design and database schema |
| [`05-api/`](05-api/) | API contracts and endpoint documentation |
| [`06-development/`](06-development/) | Local setup, coding standards, environment config |
| [`07-testing/`](07-testing/) | Test plans, test cases, QA strategy |
| [`08-deployment/`](08-deployment/) | Deployment, infrastructure, CI/CD, environments |
| [`09-project-management/`](09-project-management/) | Roadmap, meeting notes, decision records |
| [`10-changelog/`](10-changelog/) | Version history and release notes |

## Conventions

- Write in Markdown (`.md`) so everything renders on GitHub and in editors.
- Keep each doc focused on one topic; link between docs rather than duplicating content.
- Date-stamp anything time-sensitive (meeting notes, decisions) as `YYYY-MM-DD-title.md`.
- Update the relevant doc as part of the same PR that changes the behavior it describes — don't let docs drift from code.
