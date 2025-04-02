# Hybrid Cloud Architect- No. 1  *"A Farewell to Windows"*

## Overview  
In this series of blogs we will be building our very own Hybrid Cloud. 
You might ask, *what is a Hybrid Cloud?* 
I don't know. I just think it bests describes what I want to accomplish:
1. Master **Kubernetes** administration at scale
2. Build cool stuff with **Docker**
3. Learn **AWS** cloud practices

I love PC Gaming—it's what initially interested me in computers. My need for better frame rates and higher fidelity led me down a rabbit hole of chasing more performance and shinier, faster NVIDIA GPUs. 
Despite the desire, I never had the means to obtain the cutting edge gaming hardware, my first "Gaming PC" was an old Dell Optiplex my dad got from an office liquidation. It had great specs.
* **Intel Pentium 4** CPU
* **4GB of RAM** 

I slowly upgraded it piece by piece until I found it's limits, which led me to build my own PC.
* **Intel Core i5**  4 Core CPU
* **NVIDIA GTX 650 Ti** GPU 
* **8GB RAM** 
* **!!!!** 

Around this time I began experimenting with Linux Distros on old laptops I found lying around. It was good fun surfing the web and writing small programs until I spilled some sweet tea on this adorable little Sony VAIO notebook and fried the mobo. **RIP**.


Anyways, since becoming more of an adult I find less time for gaming and more time for building ... cue the reclamation of my gaming PC into a full-time Linux station. 

_"A Farewell to Windows"_ 

---
## The Hybrid Cloud Idea

With this came a thought:

_"What if, when I’m not actively developing, I put my 6-core, NVIDIA GPU-clad machine to work?"_

I started imagining:

- **Self-hosted game servers** in Docker
    
- **AI inferencing & training workloads**
- Hosted Web Applications for me and my friends
    
- A **home cloud setup** using Kubernetes orchestrating it all
    

### **The Plan:**

I’m setting up a hybrid infrastructure where:

- **AWS EC2** Instances → Acts as the control plane + general-purpose (dumb 😛) node
    
- **My on-prem workstation** → The **very special** GPU-equipped node
    

It’s the bare minimum for a **Kubernetes home cloud**, but I think it’ll get the job done.

---

## Prerequisites  
- Before you follow along, you should know some Linux, Docker, and Kubernetes basics.
- You will need all these things and more:
  - **A Linux** (Ubuntu 22.04)
  - **A container engine** (Docker) 
  - **A container orchestration service** (Kubernetes K3s)
  - **An AWS Account** (You can't use mine)
  - **An Internet** ( I have Comcast :))
  - **A keyboard** (helpful)
  - **A cup of water** (Old soup jar)
  - **A mouse** (technically optional?)

I should also mention the specs of the system I'm doing this on. She's a frankenstein of old components I bought off a college roomate and a somewhat more recent NVIDIA card I bought to play *The Witcher 3* and *Warzone*. 
* Intel Core i7-5930K 6-Core CPU
* NVIDIA GeForce RTX 2070 Super 8GB GPU
* 64 GB DDR4 RAM

The CPU is ancient with weak single-threaded performance, but I'm hoping the 6 cores and 12 threads combined with a good amount of memory will carry .. and it's a hell of a lot better than the Pentium 4!

---

## Step 1: **Setting Up the Environment**  
I am going to assume that you've installed Linux before, at least Ubuntu. 
I picked **Ubuntu 22.04** because:

- I deal with Enterprise Linux weirdness at work and do _not_ want that in my hobby projects.
    
- This is a **Kubernetes project**, _not_ a "let's debug SUSE" project. (_Yet._)

### General Setup
You know the drill. 
* Get the OS installed and running 
* Get your favorite text editor going 
* Get your git access tokens all figured out 
* Create the mounts you will be using
* Make sure the internet works? 

You will probably encounter some minor setup issues. 
_Just try things and fix them when they break._


With the basic setup covered let's move on to what we're gonna do on the local system.
### Get Docker Installed
First we will need Docker installed and our group permissions
```bash
# Update system packages 
sudo apt update && sudo apt upgrade -y 
# Install container runtime 
sudo apt install -y docker.io 
sudo systemctl enable docker 
sudo systemctl start docker 
sudo usermod -aG docker $USER
# When you update your groups you will need to log out and back in, check with
groups $USER
```

If that was successful we should see the following upon running `hello-world`
```bash
docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.
...
```

### Install Kubernetes (K3s)

I'm working with K3s cause we are ballin' on a budget and I want to save RAM for all my Chrome tabs. These offer a lightweight proxy for k8s (proxy in the literary sense) utilizing the same Kubernetes API we know and love. It's especially handy here given the single node nature of the (former) Gaming PC and a perfectly acceptable lightweight enterprise solution.

```sh
# Install kubectl 
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" 
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl 
# Install K3s  
curl -sfL https://get.k3s.io | sh -
```

### Set up Storage for Persistent Volumes
I would recommend creating a special place where you want to store data and other such things in your linux directory structure. I so happen to have created a `/data` mount on my `sdb` filesystem which is a 1TB SSD. Do whatever you want though.

```bash
# Create directory structure (-p here creates parent dirs as needed)
mkdir -p /data/k8s/volumes

# Set permissions 
sudo chmod -R 777 /data/k8s/volumes
```

---

## Step 2: **Running Your First Pod**  
Explain the next step with **context and explanation**.

1. Create a simple Kubernetes Pod definition:
   
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: my-first-pod
   spec:
     containers:
       - name: nginx
         image: nginx:latest
   ```
   
2. Apply the YAML:
   
   ```sh
   kubectl apply -f my-first-pod.yaml
   ```

---

## Step 3: **Verifying and Debugging**  
- Check if the pod is running:
  
  ```sh
  kubectl get pods
  ```
  
- If there's an error, describe how to troubleshoot.

---

## Common Issues & Fixes  
| Issue              | Solution                                      |
|-------------------|----------------------------------------------|
| `ImagePullBackOff` | Check if the image exists, run `docker pull nginx:latest` |
| `CrashLoopBackOff` | Inspect logs with `kubectl logs my-first-pod` |

---

## Next Steps  
- Suggest where to go next (e.g., "Now that we have a basic pod running, let's explore services in the next post.")

---

## Related Posts  
- **[My Learning Journal - Week 1](./2024-04-01-Week-1.md)**  
- **[Setting Up a Kubernetes Cluster](./k8s-cluster-setup.md)**  

---

## Final Thoughts  
Summarize key takeaways and encourage discussion (e.g., "Let me know in the comments if you have questions!").
