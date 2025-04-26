# 🚀 Streamlit + PostgreSQL with Docker (Mac Setup)

This project demonstrates how to deploy a **Streamlit** application that connects to a **PostgreSQL** database using **Docker** on a Mac M2.  
The application fetches and displays passenger data dynamically in a beautifully styled UI.

---

## 📂 Project Structure

```
Streamlit-Postgres-Docker/
│── Dockerfile
│── main.py
│── README.md
```

- `main.py` – Streamlit app that connects to PostgreSQL and fetches data.
- `Dockerfile` – Configuration for containerizing the Streamlit app.
- `README.md` – You are here. 😉

---

## 🛠 Setting Up PostgreSQL in Docker

### Step 1: Pull the PostgreSQL Docker Image

```bash
docker pull postgres
```
![img](https://github.com/Svadha29/DOCKER_BASICS/blob/9408e84c9fda1eb634c4c5e3c41e29db330b1557/7.%20Streamlit%20%26%20PostgreSQL%2C%20Docked/images/image.png)
---

### Step 2: Create a Docker Network

```bash
docker network create my_postgres_network
```
![img](https://github.com/Svadha29/DOCKER_BASICS/blob/9408e84c9fda1eb634c4c5e3c41e29db330b1557/7.%20Streamlit%20%26%20PostgreSQL%2C%20Docked/images/image%20copy.png)
> This allows PostgreSQL and Streamlit containers to talk to each other.

---

### Step 3: Run the PostgreSQL Container

```bash
docker run --name my_postgres_container \
  --network my_postgres_network \
  -e POSTGRES_USER=svadha \
  -e POSTGRES_PASSWORD=secret \
  -e POSTGRES_DB=testdb \
  -p 5432:5432 \
  -d postgres
```

✅ This sets up the PostgreSQL database inside Docker.

---

## 💾 Creating and Populating the Database

### Step 4: Access PostgreSQL inside the container

```bash
docker exec -it my_postgres_container psql -U svadha -d testdb
```

### Step 5: Create the `passengers` Table

```sql
CREATE TABLE passengers (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    location VARCHAR(100)
);
```

### Step 6: Insert Sample Data

```sql
INSERT INTO passengers (name, location) VALUES
('Svadha', 'Delhi'),
('Aryan', 'Jind'),
('Coach Saab', 'Atta');
```

---

## 🎨 Streamlit Application (main.py)

The Streamlit app will:

- Connect to PostgreSQL using `psycopg2`.
- Fetch data from the `passengers` table.
- Display it beautifully with custom CSS styling.

---

## 🐳 Dockerizing the Streamlit App

### Step 7: Create a Dockerfile

**Dockerfile**

```dockerfile
FROM python:3.9

WORKDIR /app

COPY main.py .

RUN pip install streamlit psycopg2

CMD ["streamlit", "run", "main.py", "--server.port=8501", "--server.address=0.0.0.0"]
```

---

## 🚀 Running the Streamlit App in Docker

### Step 8: Build the Docker Image

```bash
docker build -t streamlit_app .
```
![img](https://github.com/Svadha29/DOCKER_BASICS/blob/9408e84c9fda1eb634c4c5e3c41e29db330b1557/7.%20Streamlit%20%26%20PostgreSQL%2C%20Docked/images/image%20copy%202.png)
---

### Step 9: Run the Streamlit Container

```bash
docker run --name my_streamlit_container \
  --network my_postgres_network \
  -p 8501:8501 \
  -d streamlit_app
```

✅ Now both containers (Postgres and Streamlit) can talk via the Docker network.

---

## 🔗 Access the Streamlit Application

👉 Open your browser and go to:  
**[http://localhost:8501](http://localhost:8501)**

You will see the list of passengers beautifully displayed!
![img](https://github.com/Svadha29/DOCKER_BASICS/blob/9408e84c9fda1eb634c4c5e3c41e29db330b1557/7.%20Streamlit%20%26%20PostgreSQL%2C%20Docked/images/image%20copy%203.png)

---

## 🧹 Useful Docker Commands (for maintenance)

- List running containers:
  ```bash
  docker ps
  ```

- Stop a container:
  ```bash
  docker stop <container_name>
  ```

- Remove a container:
  ```bash
  docker rm <container_name>
  ```

- Remove an image:
  ```bash
  docker rmi <image_name>
  ```
![img](https://github.com/Svadha29/DOCKER_BASICS/blob/9408e84c9fda1eb634c4c5e3c41e29db330b1557/7.%20Streamlit%20%26%20PostgreSQL%2C%20Docked/images/image%20copy%204.png)
---

## 🎯 Summary

✅ PostgreSQL container stores passenger data.  
✅ Streamlit container fetches and displays the data.  
✅ Communication happens over `my_postgres_network`.  
✅ Everything works seamlessly on your **Mac M2** inside **VS Code**.

---

> Happy Docking! 🚢 Powered by Streamlit + PostgreSQL + Docker 💖
