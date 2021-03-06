# Pull Robot Framework container from Docker Hub and run tests 

GitHub Actions has an amazing feature for spinning up containers runtime in pipelines. I'd like to have something similar in Azure Devops. 

One Approach would be using a ready-made container and spinning it up with bash command:

~~~~
steps:
- task: Bash@3
  inputs:
    targetType: 'inline'
    script: |    
      sudo docker run --rm -v "$(Build.ArtifactStagingDirectory)/results:/opt/robotframework/reports:Z" -v "$(Build.SourcesDirectory)/tests:/opt/robotframework/tests:Z" -e BROWSER=chrome ppodgorsek/robot-framework:3.5.0                     
- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'XUnit'
    testResultsFiles: '**/*.xml'
    searchFolder: '$(Build.ArtifactStagingDirectory)'    
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(Build.ArtifactStagingDirectory)'
    ArtifactName: 'TestResults'
    publishLocation: 'Container'
~~~~	

Using https://github.com/ppodgorsek/docker-robot-framework as example here. 

Todo:

- The latest version does not run sudo user inside the container and can't write output to Azure Pipelines Agent. Need to do this within context of Pipelines Agent: 'docker run --user=1001:1001 ppodgorsek/robot-framework:latest'
- Reading test results in xunit-format would require passing the '--xunit' switch in format that the container understands.