# Azure Pihole
A repo for Azure Pihole configuration on Azure, utilizing JSON and the Template Deployment function of Azure. I originally wanted to use YAML, and had the code done for it except Azure doesn't let you configure a private network with YAML (long story, it needed some network group declaration which was complicated and then they finallly deprecated it so without this method there is literally no other way aside from manual UI methods or their AZ command line interface to get private V-nets to work). 

**Let's get started**

This code assumes that you have already configured a few things. Here's what you need. 

1. You obviously need a subscription and the associated subscription GUID.
2. You need a resource group. Let's call it 'pihole'. 
3. You need to have a configured a Virtual Network with two subnets. I call mine 'pihole-vnet'. My two subnets are 'Pub' and is 10.0.1.0/24 and 'Priv' which is 10.0.2.0/24. 
4. You need to have configured a storage account ('mystorageaccount') with two file shares. Mine are labeled as 'dnsmasq' and 'piholedata'
5. I also created a Network Security Group and bound it to the Priv subnet. It's called pihole-NSG in my implementation and referenced in the JSON as such.

Once you've done these, you can run the JSON template and parameters in Azure Template deployment, obviously swapping out for the parameters you've chosen. 

The container itself is really cheap to run - cents per day. 

**Known issues**

What this doesn't do is configure the network or set the password. 

1. Take a look at the Docker Hub image instructions for how to set the password manually. I just haven't bothered to set it in the JSON yet. Find that here: https://hub.docker.com/r/pihole/pihole 
2. Network wise because Azure's networking is pretty poor for containers, it's really difficult to use any of their services
   1. Azure Application Gateway does not support Port 53, only HTTP and HTTPS.
   2. Azure Firewall is too expensive for a project like this charging like $1.60 per hour, without even factoring traffic fees. Nope. Plus it's awful to configure. 
   3. Their NAT only does outbound not inbound (wtaf?)
   4. All other services don't support Azure Container Instances 
What that means were left with is something else like NGINX or something else. I will likely choose NGINX in a VM because container networking is painful...more to come. 
