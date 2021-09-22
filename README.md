# actions-demo

# Understanding GitHub Actions

## What is GitHub Actions?
- GitHub Actions help you automate tasks within your software development life cycle. 
- GitHub Actions are event-driven, meaning that you can run a series of commands after a specified event has occurred. 
- For example, every time someone creates a pull request for a repository, you can automatically run a command that executes a software testing script

## Components
**1.	Workflows**
- The workflow is an **automated procedure** that you add to your repository. 
- Made up of **one or more jobs** and can be scheduled or triggered by an event. 
- The workflow can be used to **build, test, package, release, or deploy** a project on GitHub.

**2.	Events**
- An event is a specific activity that triggers a workflow. 
- For example, activity can originate from GitHub when someone pushes a commit to a repository or when an issue or pull request is created. 
- You can also use the repository dispatch webhook to trigger a workflow when an external event occurs. 

Manually Triggering Workflows
- Use workflow dispatch
 
Triggering: 
Option 1: Manually from GitHub
Option 2: GitHub CLI 
```
gh workflow run workflow
```
If accept inputs
```
gh workflow run greet.yml -f name=mona -f greeting=hello -F data=@myfile.txt
```
If not on main
```
- gh workflow run workflow --ref branch-name
```
To watch: 
```
gh run watch
```

Triggering Workflows from External Events

- Use repository_dispatch
Triggering:
To trigger the custom repository_dispatch webhook event, you must **send a POST request to a GitHub API endpoint** and provide an event_type name to describe the activity type. To trigger a workflow run, you must also configure your workflow to use the repository_dispatch event.


**3.	Jobs**
- A job is a **set of steps that execute on the same runner. **
- -By default, a workflow with multiple jobs will run those jobs in parallel. 
- You can also configure a workflow to run jobs sequentially. For example, a workflow can have two sequential jobs that build and test code, where the test job is dependent on the status of the build job. If the build job fails, the test job will not run.

**4.	Steps**
- A step is an individual task that can run commands in a job
- A step can be either an **action** or a **shell command.** 
- Each step in a job executes on the same runner, allowing the actions in that job to share data with each other.

**5.	Actions**
- Actions are standalone commands that are combined into steps to create a job. 
- Actions are the **smallest portable building block of a workflow**. 
- You can create your own actions, or use actions created by the GitHub community. 
- To use an action in a workflow, you **must include it as a step**.

<img width="385" alt="Screen Shot 2021-09-22 at 10 55 25" src="https://user-images.githubusercontent.com/68419809/134266786-005c1dc0-b829-4f0e-a769-dd18bbb06979.png">


**6.	Runners**
- A runner is a server that has the GitHub Actions runner application installed. 
- You can use a runner hosted by GitHub, or you can host your own. 
- A runner listens for available jobs, runs one job at a time, and reports the progress, logs, and results back to GitHub. 
- GitHub-hosted runners are based on Ubuntu Linux, Microsoft Windows, and macOS, and each job in a workflow runs in a fresh virtual environment. 
- 
**GitHub-hosted runners:**

- Receive automatic updates for the operating system, preinstalled packages and tools, and the self-hosted runner application.
- Are managed and maintained by GitHub.
- Provide a clean instance for every job execution.
- Use free minutes on your GitHub plan, with per-minute rates applied after surpassing the free minutes.

**Self-hosted runners:**
- Receive automatic updates for the self-hosted runner application only. You are responsible for updating the operating system and all other software.
- Can use cloud services or local machines that you already pay for.
- Are customizable to your hardware, operating system, software, and security requirements.
- Don't need to have a clean instance for every job execution.
- Are free to use with GitHub Actions, but you are responsible for the cost of maintaining your runner machines.

# Syntax
https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions


GitHub Actions in the Enterprise

