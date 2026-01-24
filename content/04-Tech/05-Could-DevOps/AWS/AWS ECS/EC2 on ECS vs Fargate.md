- ECS runs on EC2（EC2 launch type)
	- With **EC2 launch type**, tasks run on EC2 instances (that you manage), and those instances run an **ECS agent**.
- ECS runs on Fargate（Fargate launch type）
	- With **Fargate**, your **tasks run on Fargate-managed compute**.

### ECS on EC2 (EC2 launch type)

**Pros**
- Lower cost at scale (often cheaper for steady, long-running workloads; you can optimize instance utilization).
- **More control & customization** (instance types, GPUs, AMIs, daemon agents, deeper networking tweaks).
- More flexibility for advanced setups (special storage, sidecar agents, privileged containers in some cases).
**Cons**
- **More operational effort**: you manage EC2 capacity, patching, upgrades, scaling, and instance failures.
- **Capacity risk**: if your EC2 cluster is full, tasks can get stuck in PENDING.
- More moving parts (Auto Scaling Groups, launch templates, AMI updates, etc.).

### ECS on Fargate (Fargate launch type)
**Pros**
- **Minimal ops / fastest setup**: no EC2 provisioning, no patching, no cluster capacity planning.
- **Easy scaling & reliability**: tasks generally start without you worrying about available instances.
- Good security model: smaller attack surface since you don’t manage host OS.
**Cons**
- Typically higher cost for always-on workloads (you pay per task CPU/memory; can be more expensive than optimized EC2).
- **Less low-level control** (can’t customize the underlying host, fewer tuning knobs).
- **Some constraints** (resource/feature limitations compared to full EC2 flexibility).