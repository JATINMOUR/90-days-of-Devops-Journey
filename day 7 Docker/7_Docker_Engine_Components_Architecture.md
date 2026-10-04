# 7. Docker Engine Components (Architecture)


### Overview Diagram

```
+-------------------------------------------------------------------+
|                        Docker Client (CLI)                        |
|                     (e.g., docker run nginx)                      |
+-------------------------------------------------------------------+
                                  |
                                  v  (REST API)
+-------------------------------------------------------------------+
|                      Docker Daemon (dockerd)                      |
|                  (Manages Images, Networks, Volumes)              |
+-------------------------------------------------------------------+
                                  |
                                  v
+-------------------------------------------------------------------+
|                            containerd                             |
|             (Pulls images, manages container lifecycle)           |
+-------------------------------------------------------------------+
                                  |
                                  v
+-------------------------------------------------------------------+
|                               runc                                |
|        (Low-level runtime: Configures namespaces & cgroups)       |
+-------------------------------------------------------------------+
                                  |
                                  v
+-------------------------------------------------------------------+
|                         Running Container                         |
+-------------------------------------------------------------------+
```

### Main Components Explained

1. **Docker CLI (Command Line Interface):**
   - User interface for issuing commands (e.g., `docker run`, `docker build`).
   - Acts as a remote control that communicates with the Daemon via REST API.

2. **Docker Daemon (`dockerd`):**
   - Background service managing high-level Docker resources: Images, Containers, Networks, and Volumes.

3. **REST API:**
   - Communication bridge transmitting instructions between the Docker CLI and `dockerd`.

4. **`containerd`:**
   - An industry-standard container runtime supervisor. Handles image pulling, storage, container execution management, and network supervision.

5. **`runc`:**
   - Low-level, lightweight tool that interacts with Linux kernel primitives (**Namespaces** for process isolation and **cgroups** for resource limits) to spawn actual container processes.
