This project demonstrates a complete CI/CD pipeline for deploying a static website to Azure Storage using Azure DevOps. The pipeline automatically deploys changes whenever code is pushed to the main branch.

✨ Features
Automated Deployment: CI/CD pipeline triggers on every git push

Azure Storage Hosting: Cost-effective static website hosting

GitHub Integration: Source code management with GitHub

YAML Pipelines: Infrastructure as Code for CI/CD

OAuth Authentication: Secure connection between GitHub and Azure DevOps

🏗️ Architecture
text
GitHub (Source Code) → Azure DevOps (CI/CD Pipeline) → Azure Storage (Static Website Hosting)
🚀 Quick Start
Prerequisites
Azure Subscription

GitHub Account

Azure DevOps Organization

Step-by-Step Implementation
1. Create Azure Resources
bash
# Create Resource Group
az group create --name static-website-rg --location eastus

# Create Storage Account
az storage account create \
    --resource-group static-website-rg \
    --name staticwebsiteaccount \
    --kind StorageV2 \
    --location eastus \
    --sku Standard_LRS
2. Set Up Static Website
Go to Azure Portal → Storage Account → Static website

Enable static website

Set index document: index.html

Set error document: 404.html

Note the Primary endpoint URL

3. Create Website Files
Create these files in your project:

index.html - Homepage

about.html - About page

contact.html - Contact page

404.html - Error page

css/styles.css - Styling

4. Set Up GitHub Repository
bash
# Initialize and push to GitHub
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/YourUsername/azure-static-website.git
git branch -M main
git push -u origin main
5. Set Up Azure DevOps
A. Create Azure Service Connection
Go to Azure DevOps Project Settings → Service connections

Click "New service connection"

Select "Azure Resource Manager"

Choose "Service principal (automatic)"

Name: azure-static-website-connection

Select your subscription and resource group

Click "Save"

B. Create GitHub Service Connection
Go to Azure DevOps Project Settings → Service connections

Click "New service connection"

Select "GitHub"

Choose "GitHub (OAuth)"

Select "AzurePipelines" OAuth configuration

Name: github-oauth-connection

Authorize with GitHub and grant repository access

Click "Save"

6. Create Azure Pipeline YAML
Create azure-pipelines.yml in your project root:

yaml
trigger:
  branches:
    include:
    - main

resources:
  repositories:
    - repository: github-repo
      type: github
      name: YourUsername/azure-static-website
      endpoint: github-oauth-connection

pool:
  vmImage: 'ubuntu-latest'

variables:
  storageAccountName: 'staticwebsiteaccount'
  resourceGroup: 'static-website-rg'

steps:
- checkout: github-repo
  displayName: 'Checkout Code from GitHub'

- script: |
    echo "Building from GitHub repository: $(Build.Repository.Name)"
    echo "Branch: $(Build.SourceBranchName)"
  displayName: 'Build Information'

- script: |
    echo "Files in repository:"
    find . -name "*.html" -o -name "*.css" | sort
  displayName: 'List Project Files'

- task: CopyFiles@2
  displayName: 'Copy Files to Artifact Directory'
  inputs:
    SourceFolder: '$(Build.SourcesDirectory)'
    Contents: |
      **/*.html
      **/*.css
      **/*.js
      **/*.png
      **/*.jpg
    TargetFolder: '$(Build.ArtifactStagingDirectory)'
    OverWrite: true

- task: AzureCLI@2
  displayName: 'Deploy to Azure Storage Static Website'
  inputs:
    azureSubscription: 'azure-static-website-connection'
    scriptType: 'bash'
    scriptLocation: 'inlineScript'
    inlineScript: |
      echo "Starting deployment to Azure Storage..."
      az storage blob upload-batch \
        --account-name "$(storageAccountName)" \
        --source "$(Build.ArtifactStagingDirectory)" \
        --destination '$web' \
        --overwrite
      echo "🎉 Deployment completed successfully!"
      echo "🌐 Your website: https://$(storageAccountName).z6.web.core.windows.net"
7. Set Permissions in Azure
Go to Azure Portal → Storage Account → Access control (IAM)

Click "Add role assignment"

Add these roles to your Azure DevOps service principal:

Storage Blob Data Contributor (for file upload)

Reader (for storage account access)

8. Create and Run Pipeline
In Azure DevOps → Pipelines → New pipeline

Select "GitHub"

Choose your repository

Select "Existing Azure Pipelines YAML file"

Path: /azure-pipelines.yml

Click "Run"

🛠️ Technologies Used
Frontend: HTML5, CSS3, JavaScript

Cloud: Azure Storage, Azure DevOps

CI/CD: Azure Pipelines, YAML

Source Control: GitHub

Authentication: OAuth 2.0

📁 Project Structure
text
azure-static-website/
├── index.html
├── about.html
├── contact.html
├── 404.html
├── css/
│   └── styles.css
├── azure-pipelines.yml
└── README.md
🔧 Pipeline Steps
Checkout: Pull code from GitHub repository

Validation: Verify required files exist

Preparation: Copy files to staging directory

Deployment: Upload files to Azure Storage $web container

Verification: Display success message with website URL

🎯 Key Learnings
Azure Storage Static Website configuration

Azure DevOps YAML pipeline creation

GitHub to Azure DevOps integration

OAuth authentication setup

Azure CLI commands for storage operations

RBAC permissions management

CI/CD best practices

🚨 Troubleshooting
Common Issues & Solutions
Permission Errors: Grant "Storage Blob Data Contributor" and "Reader" roles to service principal

OAuth Issues: Reauthorize in GitHub Settings → Applications → Authorized OAuth Apps

Container Errors: Use '$web' with quotes in Azure CLI commands

Service Connection: Verify endpoint names match in YAML and Azure DevOps

📈 Next Steps
Add custom domain

Configure CDN for better performance

Implement environment stages (Dev/Staging/Prod)

Add security scanning in pipeline

Set up monitoring and alerts

🤝 Contributing
Fork the repository

Create a feature branch

Commit your changes

Push to the branch

Create a Pull Request

📄 License
This project is licensed under the MIT License.

🙏 Acknowledgments
Azure DevOps Documentation

Azure Storage Static Websites

GitHub Integration Guides

<img width="3360" height="2100" alt="image" src="https://github.com/user-attachments/assets/788328bf-4fbc-471e-99f0-b13fea5f4d4a" />
