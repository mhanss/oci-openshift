# Terraform Deployed Resources for OpenShift on OCI

This Terraform code is specifically designed for the OpenShift on Oracle Cloud Infrastructure (OCI). It provisions resources for an OpenShift cluster running on Oracle Cloud Infrastructure.

See the following for installation instructions:

**Redhat OpenShift latest version on Oracle Cloud Infrastructure (OCI)**
- OCI documentation - https://docs.oracle.com/en-us/iaas/Content/openshift-on-oci/overview.htm
- Connected deployments using Assisted Installer: https://docs.openshift.com/container-platform/latest/installing/installing_oci/installing-oci-assisted-installer.html
- Disconnected or air gapped deployments using Agent-based Installer: https://docs.openshift.com/container-platform/latest/installing/installing_oci/installing-oci-agent-based-installer.html

## Resources Created:

- **Availability Domain**: First availability domain for the compartment.
- **Tag Namespace and Tags**:
    - Namespace: "openshift"
    - Tag values: "control-plane" and "compute"
- **Image Capabilities**:
    - Global Image Capability Schemas
    - Image Capability Schema for Openshift
    - Openshift Image Configuration
- **Shape Management**: Compute shapes for the Openshift image.
- **Network Configuration**:
    - VCN (Virtual Cloud Network)
    - Internet Gateway
    - NAT Gateway
    - Oracle Services
    - Service Gateway
    - Route Tables:
        - Public Routes
        - Private Routes
    - Security Lists:
        - Private Security List
        - Public Security List
    - Subnets:
        - Private Subnet
        - Public Subnet
- **Network Security Groups (NSGs) and Rules**:
    - NSGs:
        - Load balancers NSG
        - Cluster control plane NSG
        - Compute nodes NSG
- **Application Load Balancers**:
    - API Int
        - serves "api-int"
    - API Apps
        - serves "api" and "*.apps"
- **OCI Identity Resources**:
    - Dynamic groups
    - Policies
- **DNS Resources**:
    - oci_dns_zone
    - oci_dns_rrset (Three entries)
        - api
        - api-int
        - *.apps
- **Compute Configurations**:
    - Control Plane Instance Configuration
    - Compute Instance Configuration
- **Compute Pools**:
    - Control Plane nodes
    - Compute nodes

## Deploy OpenShift Cluster Using ABI on OCI
Follow the instructions below to deploy an OpenShift cluster using ABI on OCI**

**Prerequisites**

Ensure the following variables are set before starting the deployment:
- `zone_dns`: Set the DNS zone for the cluster. 
- `tenancy_ocid`: Your Oracle Cloud tenancy OCID. 
- `compartment_ocid`: The OCID for your OCI compartment. 
- `cluster_name`: Name of the OpenShift cluster you're deploying.
- `create_openshift_instances`: Set this variable to `false` initially.

**Run Terraform Apply**

Once the variables are set, run the following command to apply the Terraform configuration:
`terraform apply`

**Update Custom Manifests**

After running Terraform, you will need to update the `oci-ccm.yaml` custom manifest file based on the Terraform output.

**Specify Machine Network Range**

In the `install-config.yaml` file, update the machine network range to:
`machineNetwork: 10.0.16.0/20`

**Update Agent Configuration File**

Add a Rendezvous IP from the machine network range in the `agent-config.yaml` file.

**Create Agent ISO Image**

Run the following command to create the agent ISO image for the OpenShift cluster:
`openshift-install agent create image`

**Upload the Image to OCI Bucket**

- Upload the newly created image to an OCI bucket. 
- Create a pre-authenticated request URL for the uploaded image.

**Update OpenShift Image Source URI**

Add the generated image URL to the `openshift_image_source_uri` variable in the Terraform configuration.

**Enable OpenShift Instance Creation**

Enable the `create_openshift_instances variable` and set the Rendezvous IP value.

**Update Control Plane and Compute Node Values**

Make sure to update the control plane and compute node values based on your architecture and network requirements.

**Final Terraform Apply**

After making the necessary updates, run terraform apply again to finalize the deployment:
`terraform apply`

By following these steps, you will successfully deploy an OpenShift cluster on OCI using ABI. If you encounter any issues, check the Terraform logs and OCI documentation for troubleshooting.




