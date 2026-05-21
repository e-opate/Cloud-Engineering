# Cloud-Engineering
Designed and manually provisioned a secure, isolated cloud network infrastructure within AWS to validate hybrid network routing, identity isolation, and multi-tier server communication pipelines.

# Building in the Cloud: AWS Enterprise Cloud Infrastructure & Multi-VPC Network Engineering

## Meet the Engineer
Hi, I'm a Computer Science graduate with a deep love for networking infrastructure and system administration. 

When I started learning cloud architecture, I noticed a lot of people jump straight into running scripts they don't fully understand. I wanted to be different. I chose to build this entire AWS footprint **completely by hand** using the AWS Console and Linux CLI. By configuring every subnet, firewall rule, and routing table manually, I made sure I truly understand the underlying mechanics before letting code automate it for me. 

## The Blueprint: What I Built & Why

Instead of using default, insecure cloud settings, I built an isolated, multi-VPC sandbox network designed to handle real-world traffic safely.

* **Giving the CPU a Breather (S3 Integration):** Instead of forcing my EC2 server to store and serve heavy flat files, I configured a pipeline to offload them directly to **Amazon S3**. This keeps the server's storage footprint tiny, reduces CPU strain, and ensures fast, low-latency downloads for users.
* **Carving Out the Network (Subnetting):** Using my networking background, I manually mapped out a custom `10.0.0.0/16` network. I partitioned a dedicated public subnet (`10.0.1.0/24`) for web routing, saving the next block (`10.0.2.0/24`) to hide private databases down the road.
* **Locking the Perimeter (Firewalls):** I configured strict AWS Security Groups from scratch. I acted as a strict digital bouncer, dropping all incoming traffic by default except for explicit entry keys on SSH (Port 22) and web traffic (Port 80).

---

## My Favorite Breakthrough: Cracking a Routing Deadlock

The most rewarding part of this project wasn't when everything worked—it was when it broke, and I had to figure out why. While launching a validation server to test my new custom network, the machine got completely stuck in an `Initializing` loop for over 30 minutes, giving me nothing but connection timeouts.

### How I Fixed It:
1. **Ditching the Dashboard:** Rather than blindly rebooting the machine, I bypassed the AWS console overview and dug into the raw **AWS Serial Console System Logs** to see what the server was experiencing during bootup.
2. **Finding the Smoking Gun:** Deep in the logs, I spotted the background agent screaming:  
   `SSM Agent unable to acquire credentials... RequestError: send request failed`
3. **The "Aha!" Moment:** Because the error was a network timeout and not an access denial, the network engineer in me realized the packets were trapped. When you create a custom VPC, AWS defaults to a locked-down roadmap that blocks internet access. I realized I had built a beautiful custom internet route map, but skipped the crucial final step of explicitly **associating** my new subnet to it. The server was a digital island.
4. **The Resolution:** I went into the console, stapled the subnet directly to the internet-facing route table, and launched a fresh instance. Within 60 seconds, the routing table updated live, the status checks flipped to a beautiful green `2/2 checks passed`, and I executed a successful lateral SSH jump into the new environment.

---

##  Where I Am & What's Next
 * **Phase 1:** Mastering Local Linux Virtualization & CLI Essentials
 * **Phase 2:** Getting Hands-On with AWS Core Infrastructure (EC2, S3, Manual VPCs)
 * **Phase 3:** Transitioning to DevOps (Taking this exact manual architecture and automating it completely with Terraform) 
