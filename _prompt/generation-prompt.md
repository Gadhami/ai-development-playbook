# AI Dev Playbook — Generation Prompt

> This is the original prompt used to generate this template. Save it to regenerate from scratch.

---

Take a look at these two folders:

	G:\Missions\2. Mobelite\Resource-Management\.github\
	G:\Missions\2. Mobelite\github\

I want to setup/create my own plug-n-play template to use when integrating new software development teams (as a senior software developer or teamlead), and leverage such template to tremendously improve productivity while improving code quality.

Among others, I'd like to have the following:

* Extensible template. If update any content, I'd just need to do a simple commit/push (or something like that) to update the instructions

* Template content must be put here:

  G:\Missions\1. Process and Lessons\

	Create a git repo in the folder above and push it to my account at:

	  https://github.com/Gadhami

* The template must support 3 distinct profiles:

  - Frontend development only: for Angular-based projects
  - Backend development only: for .NET-based projects
  - Full-stack development: .NET for the backend, Angular for the frontend

* For greenfield .NET-based web projects, alway favor the clean architecture. I mainly use this one:

  https://github.com/jasontaylordev/cleanarchitecture

* You don't have to necessarily follow the instructions advocated by the agents in the folders above. Instead, focus on modern principles and patterns (SOLID, DRY, etc.) without being too tightly coupled to a given library/package.

* For the template, less is more. The goal is to unlock the potential of the AI LLM that's going to follow the instructions, not give it conflicting instructions that will lower the quality of its responses.

* Skip any project-specific instructions. The goal is NOT to replicate the instructions of the application (Resource Management) or the company (BigHand). Instead, I want a template that's (1) much more powerful, and (2) produces better responses from the LLM.

* I like Plandek, but keep the reporting optional (i.e. explicitly activated by me)

* Remove any specific instructions related to BigHand or Resource Management; or make the instructions generic/project-agnostic if you think they are useful as such

* Organize the repo to support this hierarchy:

  - Overall Company policy/instructions
    -> Product #1 specific policy/instructions
    -> Product #2 specific policy/instructions
    ...

* Focus on improving the quality of the template (compared to the one I gave you above), and the overall positive impact on the LLM (most likely going to be you, Opus)

* You're absolutely FREE to include any agentic AI template from anywhere, if you feel it will improve the end result

* If you're already aware of an open-source template that does what I want, you're absolutely FREE to just recommend the ready-make template and stop

* MCP servers/agents, RAG, Memory Agents, Advanced Agents, multi-agent pipelines for production-ready end-to-end workflows, etc. are all welcome if you think they will have a positive impact

* Save this instruction/prompt in a folder, in case I want to re-generate the template from scratch
