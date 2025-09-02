
---

## README.md

### What you get

* **ASG in private subnets** (min=1, desired=1, max=4).
* **Target tracking policy** keeps average CPU ≈ 30% (scales out when > 30%).
* **ALB in public subnets** distributes HTTP traffic round‑robin across healthy instances.
* **Web app** says `Hello from [IP] / [Instance ID]` and includes a **button** to kick off a 120‑second CPU stress on the serving instance.

### Deploy with CloudFormation Git sync (GitHub)

1. Push `template.yaml` to a GitHub repo.
2. In the **CloudFormation console** → **Stacks** → **Create stack** → **Sync from Git**.
3. Choose **GitHub** connection (create one if needed), select repository + branch.
4. In **Deployment file path**, enter `.cloudformation/deployment.json` (create it as shown below).
5. Finish the wizard — CloudFormation will create your stack from the template and keep it synced on every commit.

**Sample `.cloudformation/deployment.json`**

```json
{
  "region": "us-east-1",
  "templateFilePath": "template.yaml",
  "capabilities": ["CAPABILITY_NAMED_IAM"],
  "parameters": {
    "ProjectName": "AutoScalingLab"
  },
  "stackName": "AutoScalingLab"
}
```

> Adjust region/parameters as needed. Commit and push; Git sync updates the stack on each change.

### Validate & Demo

1. **Get the URL** — After stack completion, note **Outputs → `LoadBalancerDNS`**.
2. Open `http://<LoadBalancerDNS>/` — You should see: `Hello from <IP> / <InstanceId>`.
3. **Demonstrate load distribution** — Refresh several times; ALB will alternate backend instances as more are added.
4. **Trigger scale out** — Click **Start CPU Stress** a few times across browser tabs. Within a few minutes the ASG average CPU should cross 30% and **scale out** (watch **EC2 → Auto Scaling Groups → Activity** and **CloudWatch → Metrics → AutoScaling**).
5. After the scale‑out, refresh to observe different IP/Instance IDs, proving traffic is hitting all instances.

### Clean up

* Delete the stack from CloudFormation (or remove the Git sync mapping and delete).

### Notes

* NAT Gateway is created for private subnets so instances can install packages and send metrics without being publicly addressable.
* ALB algorithm evenly balances requests (round‑robin at the 7‑layer via flow‑hash/least‑outstanding requests).
* The scaling policy uses **TargetTracking** on `ASGAverageCPUUtilization` at **30%**, which is the simplest way to express “scale out when CPU exceeds 30%”.

### Deliverables to submit

* **GitHub Repository Link** — URL to the repo containing `template.yaml` and `.cloudformation/deployment.json`.
* **Load Balancer URL** — Copy the value from the `LoadBalancerDNS` output.
* **Screenshots** (optional extra marks):

  * CloudFormation stack **Outputs** showing the ALB DNS.
  * ASG **Activity** showing a scale‑out event.
  * CloudWatch metric graph of **ASGAverageCPUUtilization** rising above 30%.
  * Browser screenshots of the page served by at least two **different** instance IDs.

```
```
"# week3" 
