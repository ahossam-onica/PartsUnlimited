# ASP.NET
# Build and test ASP.NET projects.
# Add steps that publish symbols, save build artifacts, deploy, and more:
# https://docs.microsoft.com/azure/devops/pipelines/apps/aspnet/build-aspnet-4

trigger:
- master

stages:
- stage: Build
  jobs:
  - job: Build

    pool:
      name: Hosted VS2017
      demands: 
      - msbuild
      - visualstudio
      - vstest

    variables:
      solution: '**/*.sln'
      buildPlatform: 'Any CPU'
      buildConfiguration: 'Release'

    steps:
    - task: NuGetToolInstaller@1

    - task: NuGetCommand@2
      inputs:
        restoreSolution: '$(solution)'

    - task: VSBuild@1
      inputs:
        solution: '$(solution)'
        msbuildArgs: '/p:DeployOnBuild=true /p:WebPublishMethod=Package /p:PackageAsSingleFile=true /p:SkipInvalidConfigurations=true /p:PackageLocation="$(build.artifactStagingDirectory)"'
        platform: '$(buildPlatform)'
        configuration: '$(buildConfiguration)'

    - task: VSTest@2
      inputs:
        platform: '$(buildPlatform)'
        configuration: '$(buildConfiguration)'

    - task: PublishBuildArtifacts@1
      inputs:
        PathtoPublish: '$(Build.ArtifactStagingDirectory)'
        ArtifactName: 'drop'
        publishLocation: 'Container'

- stage: Deploy
  jobs:
  - job: Deploy
    pool:
      name: Hosted VS2017
    steps:

    - task: DownloadBuildArtifacts@0
      inputs:
        buildType: 'current'
        downloadType: 'single'
        downloadPath: '$(System.ArtifactsDirectory)'
        artifactName: 'drop'

    - task: AzureRmWebAppDeployment@4
      inputs:
        ConnectionType: 'AzureRM'
        azureSubscription: 'LATACC(07aef18f-f3b6-432a-8e63-ea7131d5f83b)'
        appType: 'webApp'
        WebAppName: 'webapp-223843'
        packageForLinux: '$(System.ArtifactsDirectory)/drop/*.zip'
