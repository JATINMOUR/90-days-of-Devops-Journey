# 3. Virtualization vs Containerization


| Feature | Virtualization (VMs) | Containerization (Docker) |
| :--- | :--- | :--- |
| **Architecture** | App $\rightarrow$ Bins/Libs $\rightarrow$ Guest OS $\rightarrow$ Hypervisor $\rightarrow$ Host OS $\rightarrow$ Hardware | App $\rightarrow$ Bins/Libs $\rightarrow$ Docker Engine $\rightarrow$ Host OS $\rightarrow$ Hardware |
| **OS Requirement** | Requires a dedicated **Guest OS** per Virtual Machine | Shares the **Host OS Kernel** |
| **Resource Usage** | High (Heavyweight) | Low (Lightweight) |
| **Startup Time** | Slow (Minutes) | Fast (Seconds or Milliseconds) |
| **Cost & Efficiency** | Higher infrastructure costs (Bungalow/Ghar analogy) | Lower costs & shared resources (Apartment/Flat analogy) |
