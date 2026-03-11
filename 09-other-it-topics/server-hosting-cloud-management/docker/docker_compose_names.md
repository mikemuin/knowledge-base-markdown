# Docker Compose Services and Container Name

In Docker Compose, **`service name`** and **`container name`** are related but distinct concepts. Here's how they differ and how each is used:

------

### 1. Service Name

- **Definition**: The name you define in the `docker-compose.yml` file under the top-level `services:` block.

- **Usage**:

  - **Used for networking**: Compose sets up a default network where each service name acts as a **DNS hostname**.
  - **Used in CLI** (e.g., `docker-compose logs <service-name>`, `docker-compose up <service-name>`).

- **Example**:

  ```yaml
  services:
    web:
      image: nginx
  ```

In this case, `web` is the **service name**.

  - Other containers in the same network can access this service by `http://web`.

------

### 2. Container Name

- **Definition**: The actual name assigned to the container running the service.

- **Default Behavior**: Docker Compose generates a container name based on:

  ```
  <project-name>_<service-name>_<index>
  ```

E.g., for project `myapp` and service `web`, the container name might be:
`myapp_web_1`

- **You can override it manually**:

  ```yaml
  services:
    web:
      image: nginx
      container_name: custom_web_container
  ```

- **Usage**:

  - Used for **container-level operations** like `docker exec`, `docker logs`, `docker inspect`, etc.
  - Example: `docker logs custom_web_container`

------

### Quick Comparison

| Feature              | Service Name                      | Container Name                      |
| -------------------- | --------------------------------- | ----------------------------------- |
| Defined in           | `services:` block key             | `container_name:` field (optional)  |
| Default naming       | Simple name                       | `<project>_<service>_<index>`       |
| Used for networking  | ✅ Yes (DNS hostname)              | 🚫 No (unless manually set in hosts) |
| Used in `docker` CLI | 🚫 Not directly                    | ✅ Yes (`docker ps`, etc.)           |
| Used in Compose CLI  | ✅ Yes (`docker-compose logs web`) | ✅ If explicitly named               |

------

### Recommendation

Only set `container_name` if you have a **specific reason** to do so (like legacy scripts or external dependencies). Otherwise, let Docker Compose manage it—this avoids name collisions and supports scaling (`replicas`, etc.) better.

------

Let me know if you want a diagram or a real-world example to visualize this better.