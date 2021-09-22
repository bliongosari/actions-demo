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

## Workflow Syntax
https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions

## Types of Actions
**Docker container actions**
- Docker containers package the environment with the GitHub Actions code. 
- This creates a more consistent and reliable unit of work because the consumer of the action does not need to worry about the tools or dependencies.
- A Docker container allows you to use specific versions of an operating system, dependencies, tools, and code. 
- **Ideal for for actions that must run in a specific environment configuration**, because you can customize the operating system and tools. 
- Because of the latency to build and retrieve the container, **Docker container actions are slower than JavaScript actions**.

Docker container actions can only execute on runners with a Linux operating system. 
Self-hosted runners must use a Linux operating system and have Docker installed to run Docker container actions. 

Steps:
1. Create a dockerfile
2. Creating an action metadata file (yml)
3. Write code (any language)
4. Create readme
5. Commit, tag, and push your action to GitHub


**JavaScript actions**
- JavaScript actions can run directly on a runner machine, and separate the action code from the environment used to run the code. 
- Using a JavaScript action simplifies the action code and executes faster than a Docker container action.
- To ensure your JavaScript actions are compatible with all GitHub-hosted runners (Ubuntu, Windows, and macOS), the packaged JavaScript code you write should be pure JavaScript and not rely on other binaries. 
- JavaScript actions run directly on the runner and use binaries that already exist in the virtual environment.

Steps:
1. Create action metadata (yml)
2. Install toolkit packages 
```
npm install @actions/core
npm install @actions/github
```
3. Write code
4. Create readme
5. Commit, tag, and push your action to GitHub


**Composite Actions**
- A composite action allows you to combine multiple workflow steps within one action.
- For example, you can use this feature to bundle together multiple run commands into an action, and then have a workflow that executes the bundled commands as a single step using that action.

//runners, when to use
//labels to manage runners
//security
//access
//when to use javascript/docker and why 
//creating your own actions
// github enterprise server limit
// billed 
// how ot write multi line scripts, which order

**Using private action vs public action**
**Public Action**
```
uses: octocat/hello-world-javascript-action@v1.1
```
**Private Action**
```
      - name: Checkout
        uses: actions/checkout@v2
      - name: Hello world action step
        uses: ./ 
```

**Metadata Syntax**
| Syntax | Required | Description | Examples |
|---|---|---|---|
| name | Yes | Name of your action | ```<br>name:'Hello World'<br><br>``` |
| author | No | The name of the action's author. | author: 'bliongosari' * |
| description | Yes | A short description of the action. | description: 'Greet someone and record the time' |
| inputs<br><br>- inputs.input_id (Required, String)<br>- inputs.input_id.description (Required, String)<br>- inputs.input_id.required (Required, boolean)<br>- inputs.input_id.default (Optional, String)<br>- inputs.input_id.deperecationMessage (Optional, String) | No | - Input parameters allow you to specify data that the action expects to use during runtime. <br>- GitHub stores input parameters as environment variables. <br>- Input ids with uppercase letters are converted to lowercase during runtime. <br>- We recommended using lowercase input ids. | <img width="369" alt="Screen Shot 2021-09-22 at 17 54 19" src="https://user-images.githubusercontent.com/68419809/134304434-cfedb69d-dc38-445b-b47d-9f777a675dbb.png"> |
| outputs<br><br>- outputs.output_id (Required, String)<br>- outputs.output_id.description (Required, String) | No | Output parameters allow you to declare data that an action sets. <br>Actions that run later in a workflow can use the output data set in previously run actions. | <img width="344" alt="Screen Shot 2021-09-22 at 17 54 56" src="https://user-images.githubusercontent.com/68419809/134304528-f8a94c20-b12c-4c1b-9cf6-934d3842592c.png"> |
| outputs (for composite actions)<br><br>- outputs.output_id (Required, String)<br>- outputs.output_id.description (Required, String)<br>- outputs.output_id.value (Required, string) | No | The value that the output parameter will be mapped to. You can set this to a string<br>or an expression with context. <br>For example, you can use the steps context to set the value of an output to the output value of a step. | value: ${{ steps.random-number-generator.outputs.random-id }} | <img width="545" alt="Screen Shot 2021-09-22 at 17 55 26" src="https://user-images.githubusercontent.com/68419809/134304612-8085babe-d81d-4a6e-99af-4db42d47c791.png">
| runs (javascript) <br><br>- runs.using (String, Required) (e.g. using:'node12')<br>- runs.main (String, Required)  (e.g. main: 'main.js')<br>- runs.pre (String, optional) (e.g. pre: 'setup.js')<br>- runs.pre-if (String, optional) (e.g. pre-if runner.os == 'linux')<br>- runs.post (String, optional) (e.g post: 'cleanup.js')<br>- runs.post-if (String, optional) (e.g. pre-if runner.os == 'linux') | Yes | Configures the path to the action's code and the application used to execute the code. | <img width="176" alt="Screen Shot 2021-09-22 at 17 56 59" src="https://user-images.githubusercontent.com/68419809/134304878-38c6165d-3766-424d-b189-15632a9ea680.png">  |
| runs (composite)<br><br>- runs.using (String, Required)<br>- runs.steps (String, Required)<br>- runs.steps[*].run (String, optional) <br>- runs.steps[*].shell (shell: bash, shell: cmd, shell: python, etc)<br>- runs.steps[*].id (optional)<br>- run.steps[*].env (optional)<br>- runs.steps[*].working-directory (optional)<br>- runs.steps[*].uses (optional) (action)<br>- runs.steps[*].with (optional)  | Yes |  |<img width="356" alt="Screen Shot 2021-09-22 at 17 58 14" src="https://user-images.githubusercontent.com/68419809/134305078-5c115baa-1da5-4148-974a-c2b811d94529.png">
<img width="433" alt="Screen Shot 2021-09-22 at 17 57 57" src="https://user-images.githubusercontent.com/68419809/134305032-40d34cf9-ce56-4d27-ac34-240f1fc944e2.png">
 <img width="317" alt="Screen Shot 2021-09-22 at 17 57 23" src="https://user-images.githubusercontent.com/68419809/134304963-cb55fd81-31a9-4887-9c5c-70bc2270c5bd.png"> |
| runs (docker)<br><br>- runs.using (Required)<br>- runs.image (Required)<br>- runs.pre-entrypoint<br>- runs.image<br>- runs.env<br>- runs.entrypoint<br>- post-entrypoint<br>- runs.args (Optional, [String]) | Yes |  | <img width="249" alt="Screen Shot 2021-09-22 at 17 58 40" src="https://user-images.githubusercontent.com/68419809/134305135-8d61627c-2389-42f7-a5dd-8674535a04e3.png"> |
