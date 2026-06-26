## Integrate Playwright with Azure Devops Pipeline
There are 2 options, option1 is using yaml file & option2 is without using yaml file. let's see one by one

**1. Option1 - Using YAML File**
   - Step1: Create a new project in ADO then Click on Project
    <img width="1917" height="922" alt="image" src="https://github.com/user-attachments/assets/b2320862-5d23-4e6c-858c-11ffe57fec5c" />

   - Step2: Click on Repos & Let's create new repository, Click on New reposiotry
   <img width="1917" height="875" alt="image" src="https://github.com/user-attachments/assets/6d9c63ef-bbf1-4525-bc42-7bbcd347ab58" />
   
   - Step3: Enter Repository name & Click on Create
     <img width="1921" height="863" alt="image" src="https://github.com/user-attachments/assets/c93f2727-9773-4b97-8e6d-aa14f9617ed0" />
      <img width="1906" height="867" alt="image" src="https://github.com/user-attachments/assets/c04787d3-1354-4716-b11c-c9204a24b88a" />

   - Step4: Click on Clone button and get the URL. Go to your system then clone repository.
   - Step5: Add all the playwright framework folders inside cloned repository
   <img width="1217" height="830" alt="image" src="https://github.com/user-attachments/assets/3f8f1eb5-1107-4454-88b8-34e0c86ab6f2" />

   - Step6: Push all the folders into Azure devops
    <img width="1897" height="867" alt="image" src="https://github.com/user-attachments/assets/2f24168c-2131-4db6-9340-656a335ea771" />

   - Step7: Repository is ready now, let's create pipeline. Click on Pipelines->Create Pipeline
     <img width="1912" height="872" alt="image" src="https://github.com/user-attachments/assets/1a8251a0-ae05-4dc3-982f-b4666850b639" />

   - Step8: Click on Azure Repos Git
     <img width="1917" height="855" alt="image" src="https://github.com/user-attachments/assets/ab2f67eb-1ceb-49a5-9303-68431ef35490" />

   - Step9: Select previously created repository
     <img width="1915" height="952" alt="image" src="https://github.com/user-attachments/assets/3d7f31cc-f08f-42dd-ab87-2fcb6fd3e2dc" />

   - Step10: Select Starter Pipeline
    <img width="1912" height="961" alt="image" src="https://github.com/user-attachments/assets/4af62100-c5a1-46f3-b060-2b25cb467163" />
    

   - Step11: Copy below yaml content and paste it inside azure-pipelines.yml file. 
```
trigger:
- main

pool:
  vmImage: ubuntu-latest

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '18'
  displayName: 'Install Node.js'
- script: npm ci
  displayName: 'npm ci'
- script: npx playwright install --with-deps
  displayName: 'Install Playwright browsers'
- script: npx playwright test
  displayName: 'Run Playwright tests'
  env:
    CI: 'true'
```
If you are running in self hosted agent replace pool commands
```
pool:
   name: AgentPoolName
   demands:
   - agent.name -equals AgentName
```
   - Step12: Click on Save and run
    <img width="1912" height="917" alt="image" src="https://github.com/user-attachments/assets/9e86df05-4dae-4c51-8b27-b104433201dc" />

   - Step13: You will see job queued like this.
     <img width="1917" height="922" alt="image" src="https://github.com/user-attachments/assets/9de16931-7c87-4c20-ad66-a3c528120c82" />


   - Step14: Click on Job & Verify build status.
     <img width="1917" height="632" alt="image" src="https://github.com/user-attachments/assets/feed8ed2-8de2-4812-ba52-a4dfc8f4aa9d" />

      <img width="1912" height="912" alt="image" src="https://github.com/user-attachments/assets/4b4b726d-d7e4-46f1-896a-a805c64b7c1c" />

   - Step15: Now let's Upload playwright-report folder with Azure Pipelines & Report generation
     <img width="1907" height="917" alt="image" src="https://github.com/user-attachments/assets/0f8aee0a-d589-4f4d-a93b-bc1bd6a1f2bd" />
      <img width="1912" height="865" alt="image" src="https://github.com/user-attachments/assets/c9ef493a-a308-4b45-91ee-59449f63cf29" />

     Firstly update azure-pipelines.yml file
```
trigger:
- main

pool:
  vmImage: ubuntu-latest

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '18'
  displayName: 'Install Node.js'
- script: npm ci
  displayName: 'npm ci'
- script: npx playwright install --with-deps
  displayName: 'Install Playwright browsers'
- script: npx playwright test
  displayName: 'Run Playwright tests'
  env:
    CI: 'true'

- task: PublishTestResults@2
  displayName: 'Publish test results'
  inputs:
    searchFolder: 'test-results'
    testResultsFormat: 'JUnit'
    testResultsFiles: 'e2e-junit-results.xml'
    mergeTestResults: true
    failTaskOnFailedTests: true
    testRunTitle: 'My End-To-End Tests'
  condition: succeededOrFailed()
- task: PublishPipelineArtifact@1
  inputs:
    targetPath: playwright-report
    artifact: playwright-report
    publishLocation: 'pipeline'
  condition: succeededOrFailed()
```
     
   - Step16: Verify playwright-report folder attachment & report.
       From job we can navigate into artifacts folder. Download playwright report and verify results.

   
2. Option2 - Without using YAML File
   - Step1: Repeat step 1 to 6 above from Option1
   - Step2: Click on Pipelines then click on New Pipeline
   - Step3: Click on Use the classic editor & Click on Continue
      <img width="1005" height="600" alt="image" src="https://github.com/user-attachments/assets/7f76279a-d445-4b50-a48e-dfb1f376bb34" />

   - Step4: Click on Emtpy job
      <img width="674" height="605" alt="image" src="https://github.com/user-attachments/assets/52eb82a1-382c-44da-b982-90f394600f5a" />

   - Step5: Click on + icon, Search for Node and add Node.js tool installer
   - Step6: Select just now added task and add Node v16 becuase playwright supports for Node v14 and above
     <img width="650" height="331" alt="image" src="https://github.com/user-attachments/assets/d657fd55-1cda-4e9f-a854-d0156b083c1f" />
     
   - Step7: Click on + icon, Similary add Command line task,
     Display name: Install Playwright & Dependencies
     Script: npm install && npx playwright install
      <img width="1108" height="536" alt="image" src="https://github.com/user-attachments/assets/8763db4a-4008-462a-85c4-b88f959c7045" />

      <img width="645" height="462" alt="image" src="https://github.com/user-attachments/assets/82eb439b-2c57-4671-83f2-665291bb06f8" />

     Click on Advanced-> Click on little icon(i) & select the Link. This will enable working directory for the task.
      <img width="440" height="367" alt="image" src="https://github.com/user-attachments/assets/3215f9d2-f41d-4e85-9f45-8a706af1a130" />

   - Step8: Add another task by clicking on + icon, search for npm & Add npm
     <img width="628" height="270" alt="image" src="https://github.com/user-attachments/assets/2fe6c450-18ad-45e8-bbc6-a3f228f0d369" />

     Enter Display name, Select Command as custom & Enter Command and Arguments as run tests
       <img width="635" height="494" alt="image" src="https://github.com/user-attachments/assets/4722e181-f8d8-45ef-9d37-d0e313c1cfd2" />

       In this task we are referring to the package.json file.

   - Step9: Once everthing is completed now it is a time run script. Click on Save & queue.
      <img width="503" height="401" alt="image" src="https://github.com/user-attachments/assets/019aca36-b84c-4c74-98de-188ebb579ce8" />
    Add commit message then click save & run.
   
   - Step10:Click on Job and you will see a screen like this
    <img width="701" height="422" alt="image" src="https://github.com/user-attachments/assets/b4e115c0-af01-40ac-bbf1-9205db4b8800" />


   - Step11: We can also upload playwright-report using Publish Pipeline Artifacts task
     <img width="1089" height="524" alt="image" src="https://github.com/user-attachments/assets/52ed35de-d295-45ac-9463-7c8480f79467" />

   - Step12: Let's Publish Test Results using Publish Test Results task
      <img width="1102" height="596" alt="image" src="https://github.com/user-attachments/assets/7d76cf77-a190-4379-a1c4-58484e376d55" />

   - Let's run the pipeline

   - Artifacts are published & also we have published test results
    <img width="1040" height="360" alt="image" src="https://github.com/user-attachments/assets/a8ae9318-4192-4cf6-8da2-57cc442fcbf6" />

