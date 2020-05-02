# This is how to test cloud-deploy-kubenow localy

Prerequisites
-----------

There are some tools that you need installed on your local machine, in order to provision Phenomenal-KubeNow:
- [Git](https:git-scm.com/) to clone/download the install scripts from github repo
- [Docker](https://www.docker.com/) to run the container with all other dependencies

Get Phenomenal-KubeNow
-----------

Phenomenal-KubeNow are distributed via [GitHub](http://github.com):

    # the repository contains submodules therefore `--recursive` parameter when cloning e.g.
    git clone --recursive https://github.com/phnmnl/cloud-deploy-kubenow.git

    cd cloud-deploy-kubenow

    # If you later want to pull latest version and also pull latest submodule updates:

    git pull --recurse-submodules
    git submodule update --recursive --remote

All of the commands in this documentation are meant to be run in the cloud-deploy-kubenow directory.

Deploy on Amazon Web Services
-----------
**Amazon specific prerequisites**

- You have an IAM user along with its *access key* and *security credentials* (http://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)

**Configuration**

Start by creating your configuration file: ``config.aws.sh`` There is a template that you can use for your convenience:

    mv config.aws.sh-template config.aws.sh

In this configuration file you will need to set:

*Cluster*
- **`TF_VAR_cluster_prefix`**: every resource in your tenancy will be named with this prefix

- **`TF_VAR_aws_access_key_id`**: your access key id
- **`TF_VAR_aws_secret_access_key`**: your secret access key id
- **`TF_VAR_aws_region`**: the region where your cluster will be bootstrapped (e.g. ``eu-west-1``)
- **`TF_VAR_availability_zone`**: an availability zone for your cluster (e.g. ``eu-west-1a``)

*Master configuration*
- **`TF_VAR_master_instance_type`**: an instance flavor for the master
- **`TF_VAR_master_as_edge`**:

*Node configuration*
- **`TF_VAR_node_count`**: number of Kubernetes nodes to be created (no floating IP is needed for these nodes)
- **`TF_VAR_node_instance_type`**: an instance flavor name for the Kubernetes nodes

*Gluster configuration*
- **`TF_VAR_glusternode_count`**: number of egde nodes to be created (1 - 3 depending on preferred replication factor)
- **`TF_VAR_glusternode_instance_type`**: an instance flavor for the glusternodes
- **`TF_VAR_glusternode_extra_disk_size`**: disk size of the fileserver size in GB

*Edge configuration (optional)*
- **`TF_VAR_edge_count`**: number of egde nodes to be created
- **`TF_VAR_edge_instance_type`**: an instance flavor for the edge nodes

*Cloudflare (optional)* - See: KubeNow [Cloudflare documentation.](http://kubenow.readthedocs.io/en/latest/getting_started/install-core.html#cloudflare-account-configuration)
- **`TF_VAR_use_cloudflare`**: whether you want to use cloudflare as dns provider
- **`TF_VAR_cloudflare_email`**: the mail that you used to register your Cloudflare account
- **`TF_VAR_cloudflare_token`**: an authentication token that you can generate from the Cloudflare web interface
- **`TF_VAR_cloudflare_domain`**: a zone that you created in your Cloudflare account. This typically matches your domain name (e.g. somedomain.com)
- **`TF_VAR_cloudflare_zone_id`**: a zone ID as shown in your Cloudflare account for this domain. Both domain and zone_id are needed.

*Galaxy*
- **`TF_VAR_galaxy_admin_email`**: the local galaxy admin (you?)
- **`TF_VAR_galaxy_admin_password`**: min 6 characters admin password

*Jupyter*
- **`TF_VAR_jupyter_password`**: password for your notebook


**Once you are done with your settings you are ready to deploy the cluster:**

    ./phenomenal.sh deploy aws

  when deployment is finished then you should be able to reach the services at:

    Galaxy         = http://galaxy.<your-prefix>.<yourdomain>
    Jupyter        = http://notebook.<your-prefix>.<yourdomain>
    Luigi          = http://luigi.<your-prefix>.<yourdomain>
    Kube-dashboard = http://dashboard.<your-prefix>.<yourdomain>

  and to destroy use:

    ./phenomenal.sh destroy aws


Deploy on Google Cloud Platform
-----------
**Google cloud specific prerequisites**

 - You have enabled the Google Compute Engine API: API Manager > Library > Compute Engine API > Enable

 - You have created and downloaded a service account file for your GCE project: Api manager > Credentials > Create credentials > Service account key

 - You installed python package apache-libcloud and jmespath (e.g. `sudo pip install apache-libcloud jmespath`)

**Configuration**

Start by creating your configuration file: ``config.gcp.sh`` There is a template that you can use for your convenience:

    mv config.gcp.sh-template config.gcp.sh

In this configuration file you will need to set:

*Cluster*

- **`TF_VAR_cluster_prefix`**: every resource in your tenancy will be named with this prefix

- **`TF_VAR_gce_credentials_file`**: path to your service account file
- **`TF_VAR_gce_region`**: the zone for your project (e.g. ``europe-west1-b``)
- **`TF_VAR_gce_project`**: your project id

*Master configuration*
- **`TF_VAR_master_flavor`**: an instance flavor for the master
- **`TF_VAR_master_as_edge`**:

*Node configuration*
- **`TF_VAR_node_count`**: number of Kubernetes nodes to be created (no floating IP is needed for these nodes)
- **`TF_VAR_node_flavor`**: an instance flavor name for the Kubernetes nodes

*Gluster configuration*
- **`TF_VAR_glusternode_count`**: number of egde nodes to be created (1 - 3 depending on preferred replication factor)
- **`TF_VAR_glusternode_flavor`**: an instance flavor for the glusternodes
- **`TF_VAR_glusternode_extra_disk_size`**: disk size of the fileserver size in GB

*Edge configuration (optional)*
- **`TF_VAR_edge_count`**: number of egde nodes to be created
- **`TF_VAR_edge_iflavor`**: an instance flavor for the edge nodes

*Cloudflare (optional)* - See: KubeNow [Cloudflare documentation.](http://kubenow.readthedocs.io/en/latest/getting_started/install-core.html#cloudflare-account-configuration)
- **`TF_VAR_use_cloudflare`**: wether you want to use cloudflare as dns provider
- **`TF_VAR_cloudflare_email`**: the mail that you used to register your Cloudflare account
- **`TF_VAR_cloudflare_token`**: an authentication token that you can generate from the Cloudflare web interface
- **`TF_VAR_cloudflare_domain`**: a zone that you created in your Cloudflare account. This typically matches your domain name (e.g. somedomain.com)

*Galaxy*
- **`TF_VAR_galaxy_admin_email`**: the local galaxy admin (you?)
- **`TF_VAR_galaxy_admin_password`**: min 6 characters admin password

*Jupyter*
- **`TF_VAR_jupyter_password`**: password for your notebook




**Once you are done with your settings you are ready to deploy the cluster:**

    ./phenomenal.sh deploy gcp

  when deployment is finished then you should be able to reach the services at:

    Galaxy         = http://galaxy.<your-prefix>.<yourdomain>
    Jupyter        = http://notebook.<your-prefix>.<yourdomain>
    Luigi          = http://luigi.<your-prefix>.<yourdomain>
    Kube-dashboard = http://dashboard.<your-prefix>.<yourdomain>

  and to destroy use:

    ./phenomenal.sh destroy gcp


Deploy on Openstack
-----------
**Openstack specific prerequisites**

- You have downloaded the OpenStack RC file (credentials) for your tenancy: https://docs.openstack.org/user-guide/common/cli-set-environment-variables-using-openstack-rc.html#download-and-source-the-openstack-rc-file

**Configuration**

Start by creating your configuration file: ``config.ostack.sh`` There is a template that you can use for your convenience:

    mv config.ostack.sh-template config.ostack.sh

In this configuration file you will need to set:

*Cluster*

- **`TF_VAR_cluster_prefix`**: every resource in your tenancy will be named with this prefix

- **`TF_VAR_os_credentials_file`**: your openstack credentials file: https://docs.openstack.org/user-guide/common/cli-set-environment-variables-using-openstack-rc.html#download-and-source-the-openstack-rc-file

- **`TF_VAR_floating_ip_pool`**: a floating IP pool name
- **`TF_VAR_external_network_uuid`**: the uuid of the external network in the OpenStack tenancy
- **`TF_VAR_dns_nameservers`**: (optional, only needed if you want to use other dns-servers than default 8.8.8.8 and 8.8.4.4)

*Master configuration*
- **`TF_VAR_master_flavor`**: an instance flavor for the master
- **`TF_VAR_master_as_edge`**:

*Node configuration*
- **`TF_VAR_node_count`**: number of Kubernetes nodes to be created (no floating IP is needed for these nodes)
- **`TF_VAR_node_flavor`**: an instance flavor name for the Kubernetes nodes

*Gluster configuration*
- **`TF_VAR_glusternode_count`**: number of egde nodes to be created (1 - 3 depending on preferred replication factor)
- **`TF_VAR_glusternode_flavor`**: an instance flavor for the glusternodes
- **`TF_VAR_glusternode_extra_disk_size`**: disk size of the fileserver size in GB

*Edge configuration (optional)*
- **`TF_VAR_edge_count`**: number of egde nodes to be created
- **`TF_VAR_edge_flavor`**: an instance flavor for the edge nodes

*Cloudflare (optional)* - See: KubeNow [Cloudflare documentation.](http://kubenow.readthedocs.io/en/latest/getting_started/install-core.html#cloudflare-account-configuration)
- **`TF_VAR_use_cloudflare`**: wether you want to use cloudflare as dns provider
- **`TF_VAR_cloudflare_email`**: the mail that you used to register your Cloudflare account
- **`TF_VAR_cloudflare_token`**: an authentication token that you can generate from the Cloudflare web interface
- **`TF_VAR_cloudflare_domain`**: a zone that you created in your Cloudflare account. This typically matches your domain name (e.g. somedomain.com)

*Galaxy*
- **`TF_VAR_galaxy_admin_email`**: the local galaxy admin (you?)
- **`TF_VAR_galaxy_admin_password`**: min 6 characters admin password

*Jupyter*
- **`TF_VAR_jupyter_password`**: password for your notebook



**Once you are done with your settings you are ready to deploy the cluster:**

    ./phenomenal.sh deploy ostack

  when deployment is finished then you should be able to reach the services at:

    Galaxy         = http://galaxy.<your-prefix>.<yourdomain>
    Jupyter        = http://notebook.<your-prefix>.<yourdomain>
    Luigi          = http://luigi.<your-prefix>.<yourdomain>
    Kube-dashboard = http://dashboard.<your-prefix>.<yourdomain>

  and to destroy use:

    ./phenomenal.sh destroy ostack

Deploy on Microsoft Azure
-----------
**Azure specific prerequisites**

- You have created an application API key (Service Principal) in your Microsoft Azure subscription: (https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html#creating-a-service-principal)

**Configuration**

Start by creating your configuration file: ``config.azure.sh`` There is a template that you can use for your convenience:

    mv config.azure.sh-template config.azure.sh

In this configuration file you will need to set:

*Cluster*
- **`TF_VAR_cluster_prefix`**: every resource in your tenancy will be named with this prefix
- **`TF_VAR_location`**: some Azure location (e.g. ``West Europe``)

- **`TF_VAR_subscription_id`**: your subscription id
- **`TF_VAR_client_id`**: your client id (also called appId)
- **`TF_VAR_client_secret`**: your client secret (also called password)
- **`TF_VAR_tenant_id`**: your tenant id

*Master configuration*
- **`TF_VAR_master_vm_size`**: the vm size for the master (e.g. ``Standard_DS2_v2``) (e.g. ``Standard_DS2_v2``)
- **`TF_VAR_master_as_edge`**:

*Node configuration*
- **`TF_VAR_node_count`**: number of Kubernetes nodes to be created (no floating IP is needed for these nodes)
- **`TF_VAR_node_vm_size`**: the vm size for the Kubernetes nodes (e.g. ``Standard_DS2_v2``)

*Gluster configuration*
- **`TF_VAR_glusternode_count`**: number of egde nodes to be created (1 - 3 depending on preferred replication factor)
- **`TF_VAR_glusternode_vm_size`**: the vm size for the glusternodes
- **`TF_VAR_glusternode_extra_disk_size`**: disk size of the fileserver size in GB

*Edge configuration (optional)*
- **`TF_VAR_edge_count`**: number of egde nodes to be created
- **`TF_VAR_edge_vm_size`**: the vm size for the the edge nodes

*Cloudflare (optional)* - See: KubeNow [Cloudflare documentation.](http://kubenow.readthedocs.io/en/latest/getting_started/install-core.html#cloudflare-account-configuration)
- **`TF_VAR_use_cloudflare`**: wether you want to use cloudflare as dns provider
- **`TF_VAR_cloudflare_email`**: the mail that you used to register your Cloudflare account
- **`TF_VAR_cloudflare_token`**: an authentication token that you can generate from the Cloudflare web interface
- **`TF_VAR_cloudflare_domain`**: a zone that you created in your Cloudflare account. This typically matches your domain name (e.g. somedomain.com)

*Galaxy*
- **`TF_VAR_galaxy_admin_email`**: the local galaxy admin (you?)
- **`TF_VAR_galaxy_admin_password`**: min 6 characters admin password

*Jupyter*
- **`TF_VAR_jupyter_password`**: password for your notebook


**Once you are done with your settings you are ready to deploy the cluster:**

    ./phenomenal.sh deploy azure

  when deployment is finished then you should be able to reach the services at:

    Galaxy         = http://galaxy.<your-prefix>.<yourdomain>
    Jupyter        = http://notebook.<your-prefix>.<yourdomain>
    Luigi          = http://luigi.<your-prefix>.<yourdomain>
    Kube-dashboard = http://dashboard.<your-prefix>.<yourdomain>

  and to destroy use:

    ./phenomenal.sh destroy azure
