# 🚀 Docker Volume Persistence: Bind Mounts on Mac (Apple Silicon) 🐳

## 📌 Introduction
This experiment demonstrates how to use Docker **bind mounts** with a **Linux container** to persist data beyond a container’s lifecycle.  
By mounting a local directory into a container, data remains accessible even after the container is removed.

---

## 🔧 Steps & Observations

### 🏗 Step 1: Running a Container with a Bind Mount
You executed:
```bash
docker run -dit --name alpine_with_bind_mount -v ~/docker_data:/data alpine:latest sh
```

#### 🔍 What Happened?
- Since `alpine:latest` was not found locally, Docker pulled it from the official repository.
- A new container named **alpine_with_bind_mount** was created.
- The `-v` flag mounted the local directory `~/docker_data` (your home directory) to `/data` inside the container.
- The container started a shell (`sh`) in detached mode.

---

### 📄 Step 2: Creating a File Inside the Bind Mount
Inside the container, you created a file:
```bash
docker exec -it alpine_with_bind_mount sh -c "echo 'Hello, Svadha!' > /data/testfile.txt"
```

#### 🔍 What Happened?
- The command executed a shell inside the running container.
- It created a file `testfile.txt` inside `/data` and wrote **"Hello, Svadha!"** into it.
- Since `/data` is a bind-mounted directory, the file was actually stored in `~/docker_data` on the host.

---

### ✅ Step 3: Verifying the File Exists
To check the contents:
```bash
docker exec -it alpine_with_bind_mount sh -c "cat /data/testfile.txt"
```

#### 📌 Output:
```
Hello, Svadha!
```
This confirms that the file was successfully created and accessible inside the container. 🎉

---

### 🗑 Step 4: Removing the First Container
You removed the container:
```bash
docker rm -f alpine_with_bind_mount
```

#### 🔍 What Happened?
- The container was **forcefully stopped and removed**.
- However, since `testfile.txt` was inside the bind-mounted directory, it **remained on the host system**. 🏠

---

### 🔄 Step 5: Creating a New Container with the Same Bind Mount
You started a new container:
```bash
docker run -dit --name new_alpine -v ~/docker_data:/data alpine:latest sh
```

#### 🔍 What Happened?
- A new container named **new_alpine** was created.
- The same bind-mounted directory (`~/docker_data`) was mounted to `/data`.

---

### 🔎 Step 6: Verifying File Persistence
Inside the new container, you checked if `testfile.txt` still exists:
```bash
docker exec -it new_alpine sh -c "cat /data/testfile.txt"
```

#### 📌 Output:
```
Hello, Svadha!
```
This confirms that **bind mounts persist data even after a container is removed**. 🔥

---

## 🎯 Conclusion
✅ Bind mounts allow **data persistence across multiple container instances**.  
✅ Deleting a container does **not** remove data stored in the bind-mounted directory.  
✅ Any new container with the same mount can access previous container data.  
✅ Useful for **sharing files between containers** and **persisting data beyond the container’s lifecycle**.

---
