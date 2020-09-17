# Lab 1: Google Cloud Fundamentals: Getting Started with Compute Engine

## Objectives

In this lab, you will learn how to perform the following tasks:

- Create a Compute Engine virtual machine using the Google Cloud Platform (GCP) Console.

- Create a Compute Engine virtual machine using the gcloud command-line interface.

- Connect between the two instances.

## Steps

1.  Create a virtual machine using the GCP Console.
    gcloud compute instances create my-vm-1 --machine-type "n1-standard-1" --image-project "debian-cloud" --image "debian-9-stretch-v2090213" --subnet "default" --tags http

    gcloud compute firewall-rules create allow-http --action=ALLOW --direction=INGRESS --rules=tcp:80 --target-tags=http

2.  Create a virtual machine using the gcloud command line
    gcloud config set compute/zone us-central1-b

    gcloud compute instances create "my-vm-2" --machine-type "n1-standard-1" --image-project "debian-cloud" --image "debian-9-stretch-v20190213" --subnet "default"

3.  Connect between VM instances

a) Use the ping command to confirm that my-vm-2 can reach my-vm-1 - Connect to my-vm-2:
gcloud compute ssh my-vm-2

    - ping my-vm-1 froom my-vm-2:

        ping -c 4 my-vm-1

    - Use the ssh command to open a command prompt on my-vm-1 from my-vm-2:

        ssh my-vm-1

    - At the command prrompt on my-vm-1, install the nginx web server:
        sudo apt-get install nginx-light -y

    - Use the nano text editor to add a custom message to the home page of the web server:

        sudo nano /var/www/html/index.nginx-debian.html

    - Use the arrow keys to move the cursor to the line just below the h1 header. Add text like this, and replace YOUR_NAME with your name:

        Hi from Douglas

    - Exit the editor then confim that the web server is serving your new page. At the command prompt on my-vm-1, execute this command:

        curl http://localhost/

        Result: response will again be the HTML source of the web server's home page, including your line of custom text.

    - To exit the command prompt on my-vm-1, execute this command:

        exit

    - To confirm that my-vm-2 can reach the web server on my-vm-1, at the command prompt on my-vm-2, execute this command:

        curl http://my-vm-1/

        Result: response will again be the HTML source of the web server's home page, including your line of custom text.

b) Get the external IP of my-vm-1 instance

    gcloud compute instances list --zone us-central1-a

    Paste it into the address bar of a new browser tab. You will see your web server's home page, including your custom text.

# Lab 2: Google Cloud Fundamentals: Getting Started with GKE

## Objectives

In this lab, you learn how to perform the following tasks:

- Provision a Kubernetes cluster using Kubernetes Engine.

- Deploy and manage Docker containers using kubectl.

## Steps

1. Confirm that needed APIs are enabled

- Confirm that both of these APIs are enabled:
  Kubernetes Engine API: gcloud services list --available | grep 'Kubernetes Engine API'
  Container Registry API: gcloud services list --available | grep 'Container Registry API'

2. Start a Kubernetes Engine cluster

- Place zone in MY_ZONe environment variable
  export MY_ZONE=us-central1-a

- Start a Kubernetes cluster managed by Kubernetes Engine. Name the cluster webfrontend and configure it to run 2 nodes:

  gcloud container clusters create webfrontend --zone \$MY_ZONE --num-nodes 2

- Check your installed version of Kubernetes using the kubectl version command:

  kubectl version

  Result:
  Client Version: version.Info{Major:"1", Minor:"19", GitVersion:"v1.19.0", GitCommit:"e19964183377d0ec2052d1f1fa930c4d7575bd50", GitTreeState:"clean", BuildDate:"2020-08-26T14:30:33Z", GoVersion:"go1.15",
  Compiler:"gc", Platform:"linux/amd64"}
  Server Version: version.Info{Major:"1", Minor:"15+", GitVersion:"v1.15.12-gke.2", GitCommit:"fb7add51f767aae42655d39972210dc1c5dbd4b3", GitTreeState:"clean", BuildDate:"2020-06-01T22:20:10Z", GoVersion:"go1.12.17b4", Compiler:"gc", Platform:"linux/amd64"}

- List your running nodes

  gcloud compute instances list

  Result:
  | NAME | ZONE | MACHINE_TYPE | INTERNAL_IP | EXTERNAL_IP | STATUS |
  | ----------------------------------------- | ------------- | ------------- | ----------- | ------------- | --------- |
  |gke-webfrontend-default-pool-e911037c-q17j | us-central1-a | n1-standard-1 | 10.128.0.3 | 34.72.147.15 | RUNNING |
  |gke-webfrontend-default-pool-e911037c-r9lc | us-central1-a | n1-standard-1 | 10.128.0.2 | 35.226.189.62 | RUNNING |

3. Run and deploy a container

   - Launch a single instance of the nginx container:

     kubectl create deploy nginx --image=nginx:1.17.10

   - View the pod running the nginx container:

     kubectl get pods

   - Expose the nginx container to the Internet:

     kubectl expose deployment nginx --port 80 --type LoadBalancer

   - View the new service:

     kubectl get services

     Result:

     | NAME       | TYPE         | CLUSTER-IP    | EXTERNAL-IP    | PORT(S)      | AGE   |
     | ---------- | ------------ | ------------- | -------------- | ------------ | ----- |
     | kubernetes | ClusterIP    | 10.51.240.1   | <none>         | 443/TCP      | 14m   |
     | nginx      | LoadBalancer | 10.51.241.197 | 35.239.234.187 | 80:31096/TCP | 2m56s |

   - Open a new web browser tab and paste your cluster's external IP address into the address bar. The default home page of the Nginx browser is displayed.

   - Scale up the number of pods running on your service:

     kubectl scale deployment nginx --replicas 3

   - Confirm that Kubernetes has updated the number of pods:

     kubectl get pods

   - Confirm that your external IP address has not changed:

     kubectl get services

     Result:
     | NAME | TYPE | CLUSTER-IP | EXTERNAL-IP | PORT(S) | AGE |
     | ---------- | ------------ | ------------- | -------------- | ------------ | --- |  
     | kubernetes | ClusterIP | 10.51.240.1 | <none> | 443/TCP | 14m |
     | nginx |LoadBalancer | 10.51.241.197 | 35.239.234.187 | 80:31096/TCP | 6m |

# Lab 3: Google Cloud Fundamentals: Getting Started with App Engine

## Objectives

n this lab, you learn how to perform the following tasks:

- Initialize App Engine.

- Preview an App Engine application running locally in Cloud Shell.

- Deploy an App Engine application, so that others can reach it.

- Disable an App Engine application, when you no longer want it to be visible.

## Steps

1.  Initialize App Engine

    - Initialize your App Engine app with your project and choose its region:
      gcloud app create --project=\$DEVSHELL_PROJECT_ID

    - Clone the source code repository for a sample application in the hello_world directory:
      git clone https://github.com/GoogleCloudPlatform/python-docs-samples

    - Navigate to the source directory:
      cd python-docs-samples/appengine/standard_python3/hello_world

2.  Run Hello World application locally

    - Execute the following command to download and update the packages list.

      sudo apt-get update

    - Set up a virtual environment in which you will run your application. Python virtual environments are used to isolate package installations from the system.

      sudo apt-get install virtualenv

    - If prompted [Y/n], press Y and then Enter.

      virtualenv -p python3 venv

    - Activate the virtual environment.

      source venv/bin/activate

    - Navigate to your project directory and install dependencies.

      pip install -r requirements.txt

    - Run the application:

      python main.py

    - In Cloud Shell, click Web preview (Web Preview) > Preview on port 8080 to preview the application. To access the Web preview icon, you may need to collapse the Navigation menu.

      Result: See 'Hello World' in preview screen

    - verify app is not deployed

      gcloud app versions list

3.  Deploy and run Hello World on App Engine

    - Navigate to the source directory:

      cd ~/python-docs-samples/appengine/standard_python3/hello_world

    - Deploy your Hello World application

      gcloud app deploy

      If prompted "Do you want to continue (Y/n)?", press Y and then Enter. This app deploy command uses the app.yaml file to identify project configuration.

    - Launch your browser to view the app at
      https://qwiklabs-gcp-02-7502520b3295.uc.r.appspot.com/

      gcloud app browse

    - Copy and paste the URL into a new browser window.

      Result: web page with 'Hello World'

4.  Disable the application
