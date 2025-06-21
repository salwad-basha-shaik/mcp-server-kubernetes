# Kubernetes MCP Server with Amazon Q CLI

This guide walks you through using the Kubernetes MCP server with the Amazon Q CLI tool (instead of GitHub Copilot or CLINE). Amazon Q CLI is free and fully operable via the command line.

---

## 📚 Resources
- **Amazon Q CLI Blog/Guide:** [Introducing the Enhanced CLI in Amazon Q Developer](https://aws.amazon.com/blogs/devops/introducing-the-enhanced-command-line-interface-in-amazon-q-developer/)
- **Amazon Q CLI Installation (macOS):** [Official Docs](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line-installing.html)

---

## 🚀 Installation & Setup Steps

### Step 1: Install Amazon Q CLI on macOS
- Download the .dmg file from the official docs.
- (Optional) Verify the download.
- Double-click the .dmg and drag Amazon Q to Applications.
- Open Amazon Q from Applications (GUI will open).
- Enable shell integrations (for shell command auto-completions).
- Authenticate with Builder ID or IAM Identity Center.
- Grant macOS accessibility permissions as prompted.

**If you encounter permissions issues:**
```bash
sudo chown -R $(whoami) ~/.bash_profile ~/.zshrc ~/.config/fish
chmod u+rw ~/.bash_profile ~/.zshrc
chmod -R u+rw ~/.config/fish
```
Then re-enable shell integrations.

**Alternative install:**
```bash
brew install amazon-q
```

**Validate installation:**
```bash
q --version
```

---

### Step 2: Install AWS CLI

**For Linux:**
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

**For Mac:**
```bash
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /
aws --version
```

---

### Step 3: Create AWS User & IAM Role
- Create an AWS user and activate an IAM role.
- Attach the `AmazonQFullAccess` policy (or `AdministratorAccess` temporarily if needed).
- Go to the Security Credentials tab → Create access key (for CLI use).

---

### Step 4: Configure AWS CLI
- Run:
```bash
aws configure
```
- Enter your access key, secret, region (`us-east-1`), and output format (`json`).

---

### Step 5: Test AWS CLI Connection
- Ensure your user has EC2 full access policy.
- Run:
```bash
aws ec2 describe-instances --query "Reservations[*].Instances[*].InstanceId" --output text
```
- You should see your instance IDs (e.g., `i-05b5812e0b254712f`).

---

### Step 6: Configure MCP Server in Amazon Q Developer
- Reference: [Amazon Q CLI MCP Config](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line-mcp-understanding-config.html)
- For Kubernetes MCP server, add the following to your Amazon Q developer config:

```json
{
  "mcpServers": {
    "kubernetes": {
      "command": "npx",
      "args": ["mcp-server-kubernetes"]
    }
  }
}
```
- More info: [mcp-server-kubernetes GitHub](https://github.com/Flux159/mcp-server-kubernetes)

---

### Step 7: Start Amazon Q Chat in VS Code
- Run:
```bash
q --version
q chat
```
- Amazon Q CLI will connect to Claude-4-Sonnet GPT in the background.

---

### Step 8: Validate Kubernetes MCP Server
- Run `/tools` in the chat to check if `kubernetes` is listed.
- If you see `kubernetes`, the server is running.

---

### Step 9: Set Up Kind Cluster
- Install Kind:
```bash
brew install kind
kind version
kind create cluster --name my-cluster
kubectl get nodes -o wide
kubectl get pods -A
```

#### Kind Cluster Setup Summary
- **Name:** my-cluster
- **Kubernetes Version:** v1.33.1
- **Control Plane:** Running at https://127.0.0.1:49153
- **Node:** my-cluster-control-plane (Ready)
- **Container Runtime:** containerd://2.1.1

**System Pods Running:**
- etcd, kube-apiserver, kube-controller-manager, kube-scheduler, kube-proxy, kindnet, CoreDNS (pending), local-path-provisioner (pending)

---

### Differences Between kubeadm, minikube, and Kind

#### kubeadm
- Production-grade, complex, real clusters, multi-node, highly configurable, resource intensive.

#### minikube
- Local dev, easy to use, single node, dashboard, persistent storage, VM overhead.

#### Kind
- Fast, lightweight, multi-node (as containers), great for CI/CD, no VM overhead, limited persistence.

**When to use:**
- kubeadm: Production or learning production setup
- minikube: Full-featured local dev
- Kind: Quick, lightweight clusters for testing/CI/CD

---

### Step 10: Validate with Prompts
- Example prompts:
  - `how many kubernetes pods am I running?`
  - `can you help me deploy a nginx deployment in a new namespace named qdemo`
  - `sleep 10 && kubectl get pods -n qdemo`
  - `kubectl port-forward service/nginx-deployment 8080:80 -n qdemo`

---

Everything is working as expected! 🎉
