# Project-Level GCP Integration with Lacework FortiCNAPP - Guided Configuration

### Step 1: Prerequisites for Project-Level Integration

1. **Permissions**:
   - The Google Cloud user running this setup must have the `roles/owner` permission for each project to be integrated with Lacework FortiCNAPP.

2. **Environment Setup**:
   - Use **Google Cloud Shell** or a **Terraform-supported host** with the following:
     - **Google Cloud CLI**
     - Linux tools (`curl`, `git`, `unzip`)

---

### Step 2: Start the Guided Configuration

1. **Log in** to your Lacework FortiCNAPP Console.
2. **Go to** **Settings > Integrations > Cloud accounts**.
3. Click **+ Add New** > **Google Cloud Platform** > **Guided configuration**.

---

### Step 3: Configure Basic Project-Level Integration

1. Select your **Integration Type** (e.g., Audit Log or GKE Audit Log).
2. **Provide the following information**:
   - **Project ID**: The Google Cloud Project ID where Lacework FortiCNAPP resources will be provisioned.
   - **Enable Project Level Integration** (No need to enable organization level integration).
   - **API Key**: Select an existing key or create a new one if necessary.

---

### Step 4: (Optional) Advanced Configuration

If needed, you can customize:
   - **Integration Name**: Unique names for the configuration and audit log integrations.
   - **Service Account**: Use an existing service account by providing its name and private key.

---

### Step 5: Generate and Run CLI Bundle

1. **Click Generate CLI Bundle** to create a CLI script based on your settings.
2. **Copy the generated script** and paste it into either:
   - **Google Cloud Shell**, or
   - A **Terraform-supported terminal**.

3. **Run the Script** to automatically set up and configure resources in your specified Google Cloud project.

---

### Step 6: Confirm Integration

1. After the script finishes, **refresh the Cloud accounts page** in Lacework FortiCNAPP Console.
2. The new integration should now appear and be ready for use.

---

# Project-Level GCP Integration with Lacework FortiCNAPP - Agentless Workload Scanning (Terraform)

### Step 1: Prerequisites for Project Integration

1. **Permissions**: Ensure that the Google Cloud project user has the `roles/owner` permission for the project hosting Lacework resources.

2. **Environment Setup**: If running Terraform outside Google Cloud Console, set up:
   - **Google Cloud CLI** and run:
     ```bash
     gcloud auth login
     gcloud auth application-default login
     gcloud config set project <scanning_project>
     ```
     Replace `<scanning_project>` with your project ID.

---

### Step 2: Create Terraform Files

1. **versions.tf** - Create this file to define Terraform and provider requirements 
   
```hcl
   terraform {
     required_version = ">= 1.5"
     required_providers {
       lacework = {
         source  = "lacework/lacework"
         version = "~> 1.19"
       }
     }
   }
```
2. **main.tf** - Create this file to configure the Lacework agentless scanning setup:
```
provider "lacework" {
  profile = "lw_agentless" # Update profile if needed
}

provider "google" {
  alias   = "use1"
  project = "agentless-lw-scanner" # Project where Lacework resources are hosted
  region  = "us-east1"
}

module "lacework_gcp_agentless_scanning_project_single_region" {
  source  = "lacework/agentless-scanning/gcp"
  version = "~> 2.0"
  
  providers = {
    google = google.use1
  }

  project_filter_list = [
    "monitored-project-1",
    "monitored-project-2"
  ]

  global                    = true
  regional                  = true
  organization_id           = "your-org-id"
  lacework_integration_name = "agentless_from_terraform"
}
```
### Step 3: Initialize and Apply Terraform
Initialize Terraform:
```

terraform init
```
Review Changes:
```
terraform plan
```
Review the changes Terraform will make to ensure accuracy.
Apply Changes:
```
terraform apply
```
Confirm to deploy Lacework resources for agentless scanning.

### Step 4: Verify Integration
Confirm Success:
- In the Lacework FortiCNAPP console, navigate to Settings > Integrations > Cloud accounts.
- Ensure the integration status displays as Success.
