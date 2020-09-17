#Creating Virtual Machines


##Objectives

Create several VMs instances of different types with different characteristics.
-small utility VM for administration purposes 
-a standard VM 
-a custom VM

##Task 1: Create a utility virtual machine

Run the following command:

gcloud beta compute --project=qwiklabs-gcp-00-046ec18c8db6 instances create utility-vm --zone=us-central1-c --machine-type=n1-standard-1 --subnet=default --no-address --maintenance-policy=MIGRATE --service-account=100313093755-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-10-buster-v20200910 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=utility-vm --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=any

When prompted for authorization, click to authorize.

The output should contain the following (do not copy; this is example output):

Created [https://www.googleapis.com/compute/beta/projects/qwiklabs-gcp-00-046ec18c8db6/zones/us-central1-c/instances/utility-vm].
NAME        ZONE           MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP  STATUS
utility-vm  us-central1-c  n1-standard-1               10.128.0.2               RUNNING

##Task 2: Create a Windows virtual machine

Run the following command:

gcloud beta compute --project=qwiklabs-gcp-00-046ec18c8db6 instances create vm-2 --zone=europe-west2-a --machine-type=n1-standard-2 --subnet=default --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=100313093755-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --tags=http-server,https-server --image=windows-server-2016-dc-core-v20200908 --image-project=windows-cloud --boot-disk-size=100GB --boot-disk-type=pd-standard --boot-disk-device-name=vm-2 --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=any

gcloud compute --project=qwiklabs-gcp-00-046ec18c8db6 firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server

gcloud compute --project=qwiklabs-gcp-00-046ec18c8db6 firewall-rules create default-allow-https --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:443 --source-ranges=0.0.0.0/0 --target-tags=https-server

The output should contain the following (do not copy; this is example output):

NAME  ZONE            MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
vm-2  europe-west2-a  n1-standard-2               10.154.0.2   34.105.147.150  RUNNING
Creating firewall...⠹Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-00-046ec18c8db6/global/firewalls/default-allow-http].
Creating firewall...done.
NAME                NETWORK  DIRECTION  PRIORITY  ALLOW   DENY  DISABLED
default-allow-http  default  INGRESS    1000      tcp:80        False
Creating firewall...⠹Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-00-046ec18c8db6/global/firewalls/default-allow-https].
Creating firewall...done.
NAME                 NETWORK  DIRECTION  PRIORITY  ALLOW    DENY  DISABLED
default-allow-https  default  INGRESS    1000      tcp:443        False

##Task 3: Create a custom virtual machine

Run the following command:

gcloud beta compute --project=qwiklabs-gcp-00-046ec18c8db6 instances create vm-3 --zone=us-west1-b --machine-type=e2-custom-6-32768 --subnet=default --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=100313093755-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-10-buster-v20200910 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=vm-3 --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=any

The output should contain the following (do not copy; this is example output):

Created [https://www.googleapis.com/compute/beta/projects/qwiklabs-gcp-00-046ec18c8db6/zones/us-west1-b/instances/vm-3].
NAME  ZONE        MACHINE_TYPE           PREEMPTIBLE INTERNAL_IP  EXTERNAL_IP  STATUS
vm-3  us-west1-b  custom
 		   (e2, 6 vCPU, 32.00 GiB)            10.138.0.2   35.247.20.65 RUNNING

Connect via SSH to your custom VM
run the following commands(independly):
1.	Free To see information about unused and used memory and swap space on your custom VM
2.	sudo dmidecode -t 17 to see details about the RAM installed on your VM,
3.	nproc To verify the number of processors
4.	lscpu To see details about the CPUs installed on your VM
