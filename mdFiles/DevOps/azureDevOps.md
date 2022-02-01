# Concept
* VSMs -- value streaming maps
* DevOps isn't a team or job title, a emthodology, specific piece of software, a quick fix for an org's challengesd. 
  * **DevOps is the union of people, process, and products to enable continuous delivery of value to our end users.**
* Practices
  * **Agile planning**: create backlogs, priortize the items.
  * **Continuous integration (CI)**: We'll automate how we build and test our code. We'll run that every time a team member commits changes to version control.
  * **Continuous delivery (CD):** CD is how we test, configure, and deploy from a build to a QA or production environment.
  * **Monitoring:** We'll use telemetry to get information about an application's performance and usage patterns. We can use that information to improve as we iterate.
* Azure DevOps extension: **Delivery Plan** 
* To build a C# project
  ```yml
  task: DotNetCoreCLI@2
  displayName: 'Build the project'
  inputs:
    command: 'build'
    arguments: '--no-restore --configuration Release'
    projects: '**/*.csproj'
  ```
  * Pipeline translate this task to command：
  ```yml
  dotnet build MyProject.csproj --no-restore --configuration Release
  ```
* How to pass task parameters
  * Use the **arguments** parameter
## How are tasks used in a pipeline?
* trigger for all branch
  * **- '*'**
* Publish artifact to ADO pipeline
  ```yml
  - task: PublishBuildArtifacts@1
    displayName: 'Publish Artifact: drop'
    condition: succeeded()
  ```
* Reference the variables that defined in variables section
  * **$()**
* In a template, use **${{}}** read the parameters
* Add a badge to your GitHub repository to show the status of the latest build
* Add a dashboard widget to help visualize your build history
##  Good test
* Don't test for the sake of testing
* Keep your tests short
* Ensure that your tests are repeatable
* Keep your tests focused
* Choose the right granularity

## Each task has it's own task to publish test result
* DotNetCoreCLI task parameter **publishTestResults**
* Azure Pipelines supports Cobertura and JaCoCo coverage result formats.
  
## Manage build dependencies with Azure Artifacts
## Host your own build agent in Azure Pipelines
* set up your own build agent, which can run either in the cloud or on-premises
* Build Agents:
  * A build agent is a system that performs build tasks. Think of it as a dedicated server that runs your build process.
  * The **demands** section specifies which software or capabilities you require the build machine to have.
  * Your agent can be a system that runs in the cloud or in your datacenter.
  * When you bring your own build agent, you can decide whether to perform a clean build each time or perform an incremental build
## Create a release pipeline in Azure Pipelines
  * Difference between continuous delivery and continuous deployment.
    * continuous delivery: every build you have doesn't deploy to production, but has the potential to deploy
  * In Azure Pipelines, an environment is an abstract representation of your deployment environment. 
## Azure CLI commands
  * Could run Azure Clous Shell in portal or local machine CLI
  ```console
  # Create resources group
  az group create --name tailspin-space-game-rg
  
  # Create service plan
  az appservice plan create \
  --name tailspin-space-game-asp \
  --resource-group tailspin-space-game-rg \
  --sku B1 \
  --is-linux
  
  # Create App Service Instance
  az webapp create \
  --name tailspin-space-game-web-dev-$webappsuffix \
  --resource-group tailspin-space-game-rg \
  --plan tailspin-space-game-asp \
  --runtime "DOTNET|5.0"

  # List Web Application
  az webapp list \
  --resource-group tailspin-space-game-rg \
  --query "[].{hostName: defaultHostName, state: state}" \
  --output table
  ```
## Functional Test in Azure Pipelines
* **Smoke testing** verifies the most basic functionality of your application or service.
* **Unit testing** verifies the most fundamental components of your program or library.
* **Integration testing** verifies that multiple software components work together to form a complete system.
* A **regression** occurs when existing behavior either changes or breaks after you add or change a feature.
* **Sanity testing** involves testing each major component of a piece of software to verify that the software appears to be working and can undergo more thorough testing.
* **User interface (UI) testing** verifies the behavior of an application's user interface.
* **Usability testing** is a form of manual testing that verifies an application's behavior from the user's perspective.
* **User acceptance testing (UAT)**, like usability testing, focuses on an application's behavior from the user's perspective. Unlike acceptance testing, UAT is typically done by real end users.

## UI Tests
* Use the correct locators first.
## nonfunctional tests in Azure Pipelines
* Run automated load tests by using Apache JMeter, a form of nonfunctional testing, in Azure Pipelines.
## Deployment patterns
* A **deployment pattern** is an automated way to smoothly roll out new application features to your users.