trigger: none

pool: selfhostedpool

stages:
  - stage: Terraform_Validation
    jobs:
      - job: Terraform_Init_Validate_Plan
        displayName: Terraform Init Validate Plan
        steps:
          - task: TerraformTaskV4@4
            displayName: Terraform Init
            inputs:
              provider: 'azurerm'
              command: 'init'
              backendServiceArm: 'Azure subscription 1(9117002a-2308-428d-993b-9f46dfdfd10c)'
              backendAzureRmResourceGroupName: 'infra-rg'
              backendAzureRmStorageAccountName: 'mystorageaccount199546'
              backendAzureRmContainerName: 'mycontainer'
              backendAzureRmKey: 'terraform.tfstate'

          - task: TerraformTaskV4@4
            displayName: Terraform Validate
            inputs:
              provider: 'azurerm'
              command: 'validate'

          - task: TerraformTaskV4@4
            displayName: Terraform Plan
            inputs:
              provider: 'azurerm'
              command: 'plan'
              environmentServiceNameAzureRM: 'Azure subscription 1(9117002a-2308-428d-993b-9f46dfdfd10c)'

  - stage: Trivy_Security_Scan
    dependsOn: Terraform_Validation   # <- yaha lagana hai dependsOn
    jobs:
      - job: Trivy_Security_Scanning
        displayName: Security Scanning with Trivy
        steps:
          - task: CmdLine@2
            displayName: 'Run Trivy Scan on Terraform Files'
            inputs:
              script: |
                echo "Running Trivy scan on Terraform files..."
                mkdir -p $(Build.ArtifactStagingDirectory)/reports
                
                trivy config --severity HIGH,CRITICAL --format json --output $(Build.ArtifactStagingDirectory)/reports/trivy-report.json .

                if [ ! -s $(Build.ArtifactStagingDirectory)/reports/trivy-report.json ]; then
                  echo "Error: Trivy report is empty or not generated!"
                  exit 1
                fi

          - task: PublishBuildArtifacts@1
            displayName: 'Publish Trivy HTML Report Artifact'
            inputs:
              PathtoPublish: '$(Build.ArtifactStagingDirectory)/reports'
              ArtifactName: 'trivy-Report'
              publishLocation: 'Container'

  - stage: Manual_Approval
    dependsOn: Trivy_Security_Scan   # <- yaha bhi stage level dependsOn
    jobs:
      - job: Manual_Validation
        displayName: Wait For Approval
        pool: server
        steps:
          - task: ManualValidation@1
            inputs:
              notifyUsers: 'dheerajkumar4all@gmail.com'
