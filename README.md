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

## Environment Variables
- GitHub sets default environment variables that are available to every step in a workflow run. Environment variables are case-sensitive. Commands run in actions or steps can create, read, and modify environment variables.
- To set custom environment variables, you need to specify the variables in the workflow file. 
- You can define environment variables for a **step, job, or entire workflow** using the jobs.<job_id>.steps[*].env, jobs.<job_id>.env, and env keywords.
```
jobs:
  weekday_job:
    runs-on: ubuntu-latest
    env:
      DAY_OF_WEEK: Mon
    steps:
      - name: "Hello world when it's Monday"
        if: ${{ env.DAY_OF_WEEK == 'Mon' }}
        run: echo "Hello $FIRST_NAME $middle_name $Last_Name, today is Monday!"
        env:
          FIRST_NAME: Mona
          middle_name: The
          Last_Name: Octocat
```

## Workflow Syntax
https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions
| Syntax | Description | Example |
|---|---|---|
| name | The name of your workflow |  |
| on | The name of the GitHub event that triggers the workflow. <br>You can provide a single event string, array of events,<br>array of event types | ```<br>on: push<br>on:[push, pull_request]<br>``` |
| on.event_name.types | Selects the types of activity that will trigger a workflow run. | types:[published, created, edited] |
| on.<push\|pull_request>.<branches\|tags> |  | branches, tags, branches-ignore, tags-ignore |
| on.workflow_dispatch.inputs |  | on: workflow_dispatch: inputs: abc: --- |
| on.schedule |  | cron:'30 5,17 * * *' |
| permissions |  | permissions: read-all\|write-all |
| env |  | env:<br>    SEVER: example |
| defaults<br>default.run | You can provide default shell and working-directory options for all run steps in a workflow. <br>You can also set default settings for run that are only available to a job. |  |
| concurrency | Concurrency ensures that only a single job or workflow using the same concurrency group will run at a time. | concurrency: staging_environment |
| jobs<br>jobs.job_id<br>jobs.job_id.name<br>jobs.job_id.needs job3: needs: [job1, job2]<br>jobs.job_id.runs-on [self-hosted, linux]<br>jobs.job-id.permissions actions: \|read \|write\| none<br>jobs.job-id.environment (name+url) <br>jobs.job-id.concurrency<br>jobs.job-id.outputs<br>jobs.job-id.env<br>jobs.job-id.defaults<br>jobs.job-id.defaults.run<br>jobs.job_id.if<br>jobs.job_id.steps<br>jobs.job_id.steps[*].id<br>jobs.job_id.steps[*].if<br>jobs.job_id.steps[*].name<br>jobs.job_id.steps[*].uses <br>({owner}/{repo}/{path}@{ref}) (public)<br>./.github/actions/my-action   (local)<br>docker://{image}:{tag}        (docker hub)<br>docker://{host}/{image}:{tag} (docker packages container registry)<br>jobs.job_id.steps[*].run<br>jobs.job_id.steps[*].working-directory (./temp, ./client)<br>jobs.job_id.steps[*].shell (bash, cmd, etc)<br>jobs.<job_id>.steps[*].with<br>jobs.<job_id>.steps[*].with.args (For docker container, entrypoint)<br>jobs.<job_id>.steps[*].with.entrypoint (override dockerfile entrypoint)<br>jobs.<job_id>.steps[*].env<br>jobs.<job_id>.steps[*].continue-on-error<br>jobs.<job_id>.steps[*].timeout-minutes<br>jobs.<job_id>.timeout-minutes<br>jobs.<job_id>.strategy<br>jobs.<job_id>.strategy.matrix<br>jobs.<job_id>.strategy.fail-fast<br>jobs.<job_id>.strategy.max-parallel<br>jobs.<job_id>.continue-on-error<br>jobs.<job_id>.container<br>jobs.<job_id>.container.image<br>jobs.<job_id>.container.credentials<br>jobs.<job_id>.container.env<br>jobs.<job_id>.container.ports<br>jobs.<job_id>.container.volumes<br>jobs.<job_id>.container.options<br>jobs.<job_id>.services<br>jobs.<job_id>.services.<service_id>.image<br>jobs.<job_id>.services.<service_id>.credentials<br>jobs.<job_id>.services.<service_id>.env<br>jobs.<job_id>.services.<service_id>.ports<br>jobs.<job_id>.services.<service_id>.volumes<br>jobs.<job_id>.services.<service_id>.options |  |  |

## Workflow commands
Toolkit functions: 
<img width="595" alt="Screen Shot 2021-09-22 at 22 54 40" src="https://user-images.githubusercontent.com/68419809/134347252-458d102a-03ae-425d-a29f-0c6c0b8a56ef.png">

Set output parameter 
```
echo "::set-output name=action_fruit::strawberry"
```
Set debug message
``` 
::debug::{message}
echo "::debug::Set the Octocat variable"
```
Set env variable
```
steps:
  - name: Set the value
    id: step_one
    run: |
      echo "action_state=yellow" >> $GITHUB_ENV
  - name: Use the value
    id: step_two
    run: |
      echo "${{ env.action_state }}" # This will output 'yellow'
```

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
//creating your own actions (process)
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
| runs (composite)<br><br>- runs.using (String, Required)<br>- runs.steps (String, Required)<br>- runs.steps[*].run (String, optional) <br>- runs.steps[*].shell (shell: bash, shell: cmd, shell: python, etc)<br>- runs.steps[*].id (optional)<br>- run.steps[*].env (optional)<br>- runs.steps[*].working-directory (optional)<br>- runs.steps[*].uses (optional) (action)<br>- runs.steps[*].with (optional)  | Yes |  |<img width="356" alt="Screen Shot 2021-09-22 at 17 58 14" src="https://user-images.githubusercontent.com/68419809/134305078-5c115baa-1da5-4148-974a-c2b811d94529.png"><img width="433" alt="Screen Shot 2021-09-22 at 17 57 57" src="https://user-images.githubusercontent.com/68419809/134305032-40d34cf9-ce56-4d27-ac34-240f1fc944e2.png"><img width="317" alt="Screen Shot 2021-09-22 at 17 57 23" src="https://user-images.githubusercontent.com/68419809/134304963-cb55fd81-31a9-4887-9c5c-70bc2270c5bd.png"> |
| runs (docker)<br><br>- runs.using (Required)<br>- runs.image (Required)<br>- runs.pre-entrypoint<br>- runs.image<br>- runs.env<br>- runs.entrypoint<br>- post-entrypoint<br>- runs.args (Optional, [String]) | Yes |  | <img width="249" alt="Screen Shot 2021-09-22 at 17 58 40" src="https://user-images.githubusercontent.com/68419809/134305135-8d61627c-2389-42f7-a5dd-8674535a04e3.png"> |


## Security
- Use Secrets: 
- Secrets can be configured at the organization, repository, or environment level, and allow you to store sensitive information in GitHub.
- Secrets use Libsodium sealed boxes, so that they are encrypted before reaching GitHub.
    1. Never use structured data (JSON, XML, YAML) as a secret
    2. Register all secrets used within workflows
    3. Audit how secrets are handled
    4. Use credentials that are minimally scoped (For tokens, make sure it isgranted the minimum required permissions)
    5. Audit and rotate registered secrets
    6. Consider requiring review for access to secrets
 
- Using CODEOWNERS to monitor changes:
- You can use the CODEOWNERS feature to control how changes are made to your workflow files. For example, if all your workflow files are stored in .github/workflows, you can add this directory to the code owners list, so that any proposed changes to these files will first require approval from a designated reviewer.

- Understanding the risk of script injections
- Attackers can add their own malicious content to the github context, which should be treated as potentially untrusted input. 
- These contexts typically end with body, default_branch, email, head_ref, label, message, name, page_name,ref, and title. For example: github.event.issue.title, or github.event.pull_request.body.
- You should ensure that these values do not flow directly into workflows, actions, API calls, or anywhere else where they could be interpreted as executable code. - By adopting the same defensive programming posture you would use for any other privileged application code, you can help security harden your use of GitHub Actions.
Mitigate by :
   1. Using an action instead of an inline script (recommended)
   2. Using an intermediate environment variable
   3. Using CodeQL to analyze your code
   4. Restricting permissions for tokens

- Using third-party actions
Mitigate by:
   1. Pin actions to a full length commit SHA
   2. Audit the source code of the action
   3. Pin actions to a tag only if you trust the creator

## Potential Impacts of Security Flaws
1. Accessing secrets
2. Exfiltrating data from a runner
3. Stealing the job's GITHUB_TOKEN
4. Modifying the contents of a repository

## Accessing Repository Data Best Approaches (From best to worst)
1. GitHub Token
2. Repository deploy key
3. GitHub App tokens
4. Personal access tokens
5. SSH keys on a user account

## Hardening self hosted runners
- Self-hosted runners on GitHub do not have guarantees around running in ephemeral clean virtual machines, and can be persistently compromised by untrusted code in a workflow.
- Self-hosted runners **should almost never be used for public repositories** on GitHub, because any user can open pull requests against the repository and compromise the environment. 
- Similarly, **be cautious when using self-hosted runners on private repositories**, as anyone who can fork the repository and open a pull request are able to compromise the self-hosted runner environment, including gaining access to secrets and the GITHUB_TOKEN
- In an organization level, to help reduce the scope of a compromise, you can create boundaries by organizing your self-hosted runners into separate groups.

# Secrets
-  GitHub uses a libsodium sealed box to help ensure that secrets are encrypted
-   Organization-level secrets let you share secrets between multiple repositories, which reduces the need for creating duplicate secrets.

## Naming Secrets
- Secret names can only contain alphanumeric characters ([a-z], [A-Z], [0-9]) or underscores (_). Spaces are not allowed.
- Secret names must not start with the GITHUB_ prefix.
- Secret names must not start with a number.
- Secret names are not case-sensitive.
- Secret names must be unique at the level they are created at.

If a secret with the same name exists at multiple levels, the secret at the lower level takes precedence.

# GitHub Hosted Runners
- A GitHub-hosted runner is a **virtual machine hosted by GitHub** with the GitHub Actions runner application installed. 
- GitHub offers runners with **Linux, Windows, and macOS **operating systems.
- When you use a GitHub-hosted runner, machine maintenance and upgrades are taken care of for you. 
- You can run workflows directly on the **virtual machine or in a Docker container.**
**Hardware specifications:**
- Hardware specification for Windows and Linux virtual machines:
- 2-core CPU
- 7 GB of RAM memory
- 14 GB of SSD disk space
- Hardware specification for macOS virtual machines:
- 3-core CPU
- 14 GB of RAM memory
- 14 GB of SSD disk space

# Self-Hosted Runners
- Self-hosted runners offer more control of hardware, operating system, and software tools than GitHub-hosted runners provide. 
- With self-hosted runners, you can choose to create a custom hardware configuration with more processing power or memory to run larger jobs, install software available on your local network, and choose an operating system not offered by GitHub-hosted runners. 
- Self-hosted runners can be physical, virtual, in a container, on-premises, or in a cloud.
- Your runner machine connects to GitHub using the GitHub Actions self-hosted runner application.
- A self-hosted runner is automatically removed from GitHub if it has not connected to GitHub Actions for more than 30 days.

Proxy servers in self hoster runners
<img width="879" alt="Screen Shot 2021-09-22 at 21 45 56" src="https://user-images.githubusercontent.com/68419809/134337878-eed02fd3-450d-4a4b-80e7-f6ea7db4231b.png">


# Differences between GitHub-hosted and self-hosted runners
GitHub-hosted runners offer a quicker, simpler way to run your workflows, while self-hosted runners are a highly configurable way to run workflows in your own custom environment.

## GitHub-hosted runners
- Receive automatic updates for the operating system, preinstalled packages and tools, and the self-hosted runner application.
- Are managed and maintained by GitHub.
- Provide a clean instance for every job execution.
- Use free minutes on your GitHub plan, with per-minute rates applied after surpassing the free minutes.

## Self-hosted runners
- Receive automatic updates for the self-hosted runner application only. You are responsible for updating the operating system and all other software.
- Can use cloud services or local machines that you already pay for.
- Are customizable to your hardware, operating system, software, and security requirements.
- Don't need to have a clean instance for every job execution.
- Are free to use with GitHub Actions, but you are responsible for the cost of maintaining your runner machines.


# Excercises

# Managing GitHub in Enterprise
 - Enterprise level:
 -  Configure github actions use policy
 - Manually sync public actions for Enterprise Server (However already have automatically)

- Organization level:
- Document corporate standards
- Create workflow templates (yml, and json metadata)





