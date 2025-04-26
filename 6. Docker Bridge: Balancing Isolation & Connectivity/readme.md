# 🚀 Docker Bridge: Balancing Isolation & Connectivity

## 📌 Objective
The goal of this exercise is to explore and demonstrate **network isolation** in Docker containers. We will examine how containers within the same **custom bridge network** can communicate, while those on different networks remain **isolated**. Understanding this is crucial for securing microservices and containerized applications.  

---

## 🌐 Introduction to Docker Networking
Docker networking is fundamental for **containerized applications**, allowing containers to communicate while ensuring **security and isolation**. Docker provides several networking options:

### 🔹 Types of Docker Networks:
- **Bridge Network (Default)** – Allows communication between containers using internal IPs unless restricted.
- **Custom Bridge Network** – Offers better control and supports name-based resolution.
- **Host Network** – Attaches containers directly to the host’s network stack.
- **Overlay Network** – Enables communication across multiple hosts (Docker Swarm).
- **Macvlan Network** – Assigns a MAC address to each container, making them appear as separate devices.
- **None Network** – Completely disables networking.

For this demonstration, we focus on the **custom bridge network**, which improves control and **network isolation**.

---

## ⚡ Why Use a Custom Bridge Network?
A **custom bridge network** offers several advantages:
✅ **Improved Security** – Containers on different networks are isolated by default.  
✅ **Better Performance** – Direct communication without host networking stack overhead.  
✅ **DNS-Based Resolution** – Containers communicate via names instead of IPs.  
✅ **Greater Control** – Define specific **subnets, IP ranges, and gateways**.

We will create a **custom bridge network** called `svadha-bridge` and connect multiple containers to it.

---

## 🔧 1. Creating a Custom Bridge Network
In VS Code Terminal on your Mac (make sure Docker Desktop is running):
```bash
docker network create --driver bridge --subnet 172.20.0.0/16 --ip-range 172.20.240.0/20 svadha-bridge
```

### 🔍 Explanation:
- `--driver bridge` → Uses the default **bridge network mode**.
- `--subnet 172.20.0.0/16` → Defines the network’s **IP range**.
- `--ip-range 172.20.240.0/20` → Allocates IPs **dynamically** within a smaller block.

---

## 🚀 2. Running Containers in the Custom Network

### Run **Redis Container** (`svadha-database`):
```bash
docker run -itd --net=svadha-bridge --name=svadha-database redis
```

### Run **BusyBox Container** (`svadha-server-A`):
```bash
docker run -itd --net=svadha-bridge --name=svadha-server-A busybox
```

> ⚡ Tip: Use VS Code's integrated Terminal (⌘ + `) for quick execution.

### 📌 Check Container IPs:
```bash
docker network inspect svadha-bridge
```
Expected Output:
```
 svadha-database: 172.20.240.1
 svadha-server-A: 172.20.240.2
```

---

## 📔 3. Testing Communication Between Containers

### Ping from **svadha-database** to **svadha-server-A**:
```bash
docker exec -it svadha-database ping 172.20.240.2
```

### Ping from **svadha-server-A** to **svadha-database**:
```bash
docker exec -it svadha-server-A ping 172.20.240.1
```
✅ Expected Outcome: Both containers should successfully **ping** each other.

---

## 🚧 4. Demonstrating Network Isolation with a Third Container

Now, run a new container **without specifying** any network (it defaults to `bridge`):

```bash
docker run -itd --name=svadha-server-B busybox
```

### 📌 Get the IP of `svadha-server-B`:
```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' svadha-server-B
```
(Example IP: `172.17.0.2`)

---

## ❌ 5. Testing Communication Between Different Networks

Ping from `svadha-database` to `svadha-server-B`:
```bash
docker exec -it svadha-database ping 172.17.0.2
```
🚨 **Expected Outcome:** The ping should **fail** since they're on different networks.

---

## 🔍 6. Confirming Network Isolation

Inspect both networks:

```bash
docker network inspect svadha-bridge
docker network inspect bridge
```

✅ `svadha-bridge` should contain `svadha-database` and `svadha-server-A`.  
✅ Default `bridge` should contain `svadha-server-B`.

---

## 🏆 Conclusion
- **Containers within the same network** can communicate.
- **Containers across different networks** are isolated **by default**.
- Docker’s **networking model** provides a balance between **connectivity** and **security**.
