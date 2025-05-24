
# 07.Storage, Volumes and file system in Docker
In the realm of containerized applications, data persistence is paramount. This guide delves into Docker storage, encompassing volumes, the container file system, storage classes, best practices, and use cases, equipping you to manage data effectively within your containerized environment.

### Docker Volume

A Docker volume is a special directory within containers for persistent data storage, independent of the container lifecycle. It allows data to persist even when containers are stopped or removed.

### Storage

Storage in Docker encompasses methods and options for managing data storage. This includes volumes, bind mounts, and storage drivers, providing flexibility and control over how data is stored and accessed within containers.

### File System

The file system in Docker refers to the hierarchical structure for organizing files and directories within Docker containers. It is isolated from the host system, ensuring that changes made within containers do not affect the host. Docker's file system facilitates efficient management and isolation of containerized applications.

## Understanding Storage Concepts

### Docker Volumes (Recommended for Persistence):

**Concept**: Independent storage locations managed by Docker, separate from container layers and the host file system.

**Benefits**:
- **Persistence**: Data written to volumes survives container restarts, upgrades, and migrations.
- **Portability**: Volumes are not tied to specific hosts, enhancing application portability across environments.
- **Scalability**: Volumes can be shared and accessed by multiple containers, facilitating data exchange in multi-container setups.

**Best Practices**:
- **Clear Naming**: Use descriptive names (e.g., mysql-data, app-config) for better organization.
- **Separate Configuration**: Store application configuration files outside volumes (e.g., container layer or configuration management tool) for easier updates.
- **Regular Backups**: Back up volumes regularly to prevent data loss due to unforeseen circumstances.
- **Docker Compose Integration**: Manage volumes easily within your docker-compose.yml file for multi-container applications.

**Example Use Case**: Persist database data by creating a volume for the database's data directory and mounting it to the container running the database software.

## Docker File System (Ephemeral by Default):

**Concept**: A hierarchical structure for organizing files and directories within Docker containers. It is isolated from the host system's file system, ensuring changes made inside containers do not affect the host.

**Key Points**:
- **Layered**: Docker images are built in layers, with each layer contributing to the final file system within the container.
- **Ephemeral**: By default, the container's file system is not persistent. Data written to it is lost when the container stops. Volumes address this by providing persistent storage.

### Storage Drivers:

**Concept**: Software components that interact with the underlying storage system (disk, SAN, NAS) to create, manage, and optimize container storage layers and volumes.

**Common Drivers**: overlay2 (default), devicemapper, aufs (deprecated).

### Storage Classes (Kubernetes):

**Concept**: (Applicable to Kubernetes environments) A way to define storage provisioning options with characteristics like performance, durability, and cost.

**Benefits**:
- **Abstraction**: Decouples applications from specific storage details.
- **Flexibility**: Enables choosing the most suitable storage type based on application requirements.

**Demo**:
- **PersistentVolume (PV)**: Defines a storage resource in the cluster, such as a network volume, that exists independently of any individual pod that uses it.
- **PersistentVolumeClaim (PVC)**: Requests a specific size and access mode for a persistent volume. Pods use PVCs to request persistent storage.
  
```yaml
# Example PersistentVolume (PV) YAML
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: standard
  hostPath:
    path: "/mnt/data"
---
# Example PersistentVolumeClaim (PVC) YAML
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: standard
```
### Demo - Creating and Using a Volume:
### Create a volume
```bash
docker volume create my-data-volume
```
### Run a container with a volume mount
```bash
docker run -d --name webapp-server -v my-data-volume:/app/data ubuntu sleep infinity
```
### This mounts the volume my-data-volume to the /app/data directory within the container.

### Create data inside the container
```bash
cd /app/data
echo "Hello, Welcome to Azure DevOps Pulse! This is a persistent volume test!" > my-data.txt
exit
```
### Verify data persistence

### Stop the container
```bash
docker stop webapp-server
```
### Start the container again
```bash
docker start webapp-server
```
### Re-enter the container
```bash
docker exec -it webapp-server bash
```
### Verify data inside the container
```bash
cd /app/data
cat my-data.txt
```
You should see "Hello, Welcome to Azure DevOps Pulse! This is a persistent volume test!" displayed, confirming data persistence after the container restart.

## Docker Volume Commands

- **Create a volume**: `docker volume create <volume-name>` (e.g., `docker volume create my-data-volume`)
- **List volumes**: `docker volume ls`
- **Inspect a volume**: `docker volume inspect <volume-name>`
- **Mount a volume**: When running a container, use the `-v` flag:
  ```bash
  docker run -it --name <container-name> -v <volume-name>:/<container-path> <image-name>
  ```
  **Remove a volume (caution: data loss):** `docker volume rm <volume-name>` (Use with caution!)

  ## Additional Considerations

- **Bind Mounts (Use with Caution)**: While not ideal for production due to portability limitations, bind mounts map a directory on the host machine's filesystem directly into a container's filesystem. Data written to the mounted directory persists on the host.

- **Security**: Consider volume encryption (e.g., dm-crypt or LUKS) for handling sensitive data.

- **Advanced Storage Solutions**: For large deployments, leverage distributed storage systems like Ceph or GlusterFS with Docker Swarm or Kubernetes.

By understanding and effectively utilizing these concepts, you can ensure your containerized applications have persistent data storage, promoting flexibility, scalability, and a smoother development and deployment experience.



# Congratulations on completing the Docker learning journey for absolute beginners! 🎉

Thank you for embarking on this journey with us. We hope you found the content valuable and gained a solid foundation in Docker fundamentals.

As you continue your learning journey, remember to explore more advanced topics, dive deeper into specific areas of interest, and keep experimenting with Docker to reinforce your knowledge.

Thank you once again for choosing to learn with us. Wishing you all the best in your future endeavors with Docker and beyond!

Enjoying the content? Subscribe to [Azure DevOps Pulse](https://www.youtube.com/@AzureDevOpsPulse) on YouTube for more DevOps topics!

Happy Dockerizing! 🐳✨
