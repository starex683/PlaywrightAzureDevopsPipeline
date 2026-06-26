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


   - Step15: Now let's Upload playwright-report folder with Azure Pipelines & Report generation
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
    ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/54aaf4b4-7715-435d-b96a-7a19c23fa384)
    ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/5ed6c543-4162-43a9-9f21-9643d7f52438)

   
2. Option2 - Without using YAML File
   - Step1: Repeat step 1 to 6 above from Option1
   - Step2: Click on Pipelines then click on New Pipeline
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/1f753af4-881e-495d-a7dd-8c9163de97ff)

   - Step3: Click on Use the classic editor & Click on Continue
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/499f6cf4-0458-4aba-813a-ad131cea4b02)
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/332011f7-0ae4-46ce-a9c2-3c2a9d66f599)

   - Step4: Click on Emtpy job
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/d84dda85-cbc0-4c9f-9147-bc1de36823c4)

   - Step5: Click on + icon, Search for Node and add Node.js tool installer
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/73d32c4d-2cd1-4f78-beb7-ec1bf5f5138a)
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/64fcf35f-1200-4ccf-b34c-d53072728ced)


   - Step6: Select just now added task and add Node v16 becuase playwright supports for Node v14 and above
   - ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/aa804427-464c-434f-b4e4-27547b245bd9)

   - Step7: Click on + icon, Similary add Command line task,
     Display name: Install Playwright & Dependencies
     Script: npm install && npx playwright install
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/df63a628-ccb4-4709-8c2a-166358dc5264)
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/70991c9e-ad21-4ab8-978e-ba02d0f5971f)

     Click on Advanced-> Click on little icon(i) & select the Link. This will enable working directory for the task.
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/1d6dc42d-e720-4446-b1f7-4589f105ff04)

   - Step8: Add another task by clicking on + icon, search for npm & Add npm
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/49eadf73-d640-4c7d-8ea6-730f2291d503)

     Enter Display name, Select Command as custom & Enter Command and Arguments as run tests
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/f055ace0-8cdb-46e7-9f24-33c808eef4ee)

     In this task we are referring to the package.json file.
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/6074a566-6efb-46a7-ad37-2108eed90bf8)

   - Step9: Once everthing is completed now it is a time run script. Click on Save & queue.
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/112334f9-6adb-43a5-b2d4-41f364c7527d)

    Add commit message then click save & run.
   
   - Step10: It looks like this
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/ae48637e-d0bf-4a32-8d93-2ea251301068)

     Click on Job and you will see a screen like this
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/b135a6c0-039c-4b90-934c-849b35e47cbc)

   - Step11: We can also upload playwright-report using Publish Pipeline Artifacts task
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/138a4af7-addd-4498-8296-64468c957610)

   - Step12: Let's Publish Test Results using Publish Test Results task
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/f0ea2efe-3509-4e48-baa5-01a2ec9f283d)


   - Let's run the pipeline
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/575518de-feed-4267-b7f1-290e4ad76903)

   - Artifacts are published & also we have published test results
     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/d575766a-2f20-42f2-a1e3-aa5f71259823)

     ![image](https://github.com/BakkappaN/PlaywrightTutorialFullCourse/assets/22426896/45821475-feb2-4b16-a9ae-126c55cf20a1)
