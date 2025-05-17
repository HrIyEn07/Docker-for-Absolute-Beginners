# 02.Image Creation, Management, and Registry with Examples

Let's delve into the world of Docker images with examples for various application frameworks, building upon the concepts from the previous section.

### Step-1: Create a Dockerfile, named as `Dockerfile` in your project directory.
Here we have 3 Dockerfiles such as Python, ASP.net and Java based application, you can choose any according to your requirements. 

**01.Python Flask Application:**
 ```Dockerfile
# Use a minimal Python base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy your application code
COPY . .

# Install dependencies (replace requirements.txt with your actual file)
RUN pip install -r requirements.txt

# Expose the port your application runs on (replace 5000 with your port)
EXPOSE 5000

# Command to run your application (replace app.py with your main script)
CMD ["python", "app.py"]
```

**Explanation:** This Dockerfile defines the steps to create a Docker image for a Python application:

- **Base Image:** It starts with a minimal Python base image (`python:3.9-slim`), providing a lightweight environment for running Python applications.

- **Working Directory:** Sets the working directory inside the container to `/app`, where the application code will be copied.

- **Copy Application Code:** Copies the application code from the current directory on the host into the `/app` directory inside the container.

- **Install Dependencies:** Installs the dependencies listed in the `requirements.txt` file using pip, ensuring that all required Python packages are available inside the container.

- **Expose Port:** Exposes port `5000` on the container, indicating that the application will be listening for incoming connections on this port.

- **Command to Run Application:** Specifies the command to run the application. In this case, it runs the `app.py` script using the Python interpreter.

Overall, this Dockerfile sets up a containerized environment for a Python application, ensuring that all necessary dependencies are installed and the application is ready to run.

**02.ASP.NET Core Web Application:**
 ```Dockerfile
# Use the official .NET SDK image for building
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /app

# Copy the project file and restore dependencies
COPY WebApp-ASP.Net core.csproj .
RUN dotnet restore

# Copy the remaining source code
COPY . .

# Build the application
RUN dotnet publish -c Release -o out

# Use a lighter runtime image for the final image
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS runtime
WORKDIR /app

# Copy the built application from the build image
COPY --from=build /app/out .

# Expose the port the app will run on
EXPOSE 80

# Command to run the application
ENTRYPOINT ["dotnet", "WebApp-ASP.Net-core.dll"]
```
**Explanation: Dockerfile for ASP.NET Core Application**

- **Base Image:** Utilizes Microsoft's official .NET SDK 6.0 image (`mcr.microsoft.com/dotnet/sdk:6.0`) as the base for building the application.
- **Working Directory:** Sets the container's working directory to `/app` where the application files will be processed during the build stage.
- **Dependency Restoration:** Copies the project file (`WebApp-ASP.Net core.csproj`) into the container's working directory and restores the project's dependencies using the `dotnet restore` command.
- **Source Code Copy:** Copies the remaining source code from the host into the container's working directory.
- **Application Build:** Builds the application in Release configuration and publishes it to the `/app/out` directory within the container.
- **Base Image:** Utilizes Microsoft's official ASP.NET Core 6.0 image (`mcr.microsoft.com/dotnet/aspnet:6.0`) as the base for the final runtime environment.
- **Working Directory:** Sets the container's working directory to `/app`.
- **Application Copy:** Copies the built application from the build stage into the current runtime stage.
- **Port Exposition:** Exposes port 80 inside the container to allow external connections to the ASP.NET Core application.
- **Command to Run Application:** Specifies the command to run the ASP.NET Core application using the `dotnet` tool. The `WebApp-ASP.Net-core.dll` assembly is executed as the entry point for the application.


**03.Java Spring Boot Application:**
 ```Dockerfile
# Use a Java base image
FROM openjdk:17-slim

# Set working directory
WORKDIR /app

# Copy your project (replace with your project directory)
COPY . .

# Install Maven (package manager for Java)
RUN apt-get update && apt-get install -y maven

# Compile and package the application using Maven (replace pom.xml with your file name)
RUN mvn package

# Copy the compiled JAR file to a location accessible at runtime
COPY target/*.jar app.jar

# Expose port your application listens on (replace 8080 with your port)
EXPOSE 8080

# Command to run the application (replace java -jar app.jar with your actual command)
CMD ["java", "-jar", "app.jar"]
```
**Explanation:** Dockerfile for Java Spring Boot Application

- **Base Image:** Starts with the official OpenJDK Java 17 slim image (openjdk:17-slim) for a minimal Java environment.
- **Working Directory:** Sets the working directory inside the container to /app.
- **Copy Project:** Copies the Java Spring Boot project from the host to the /app directory in the container.
- **Install Maven:** Updates package lists and installs Maven (Java package manager) for building the application.
- **Compile and Package Application:** Runs Maven to compile and package the Spring Boot application using the pom.xml file.
- **Copy Compiled JAR:** Copies the compiled JAR file from the target directory to the root directory of the container and renames it to app.jar.
- **Expose Port:** Exposes port 8080 inside the container for incoming connections.
- **Command to Run Application:** Specifies the command to run the application, running the JAR file using the `java -jar` command.

This Dockerfile ensures that the Java Spring Boot application is built, packaged, and ready to run inside a Docker container.

### Step-2: Build the Docker Image:
This focuses on building the Docker image using the instructions provided in a Dockerfile.
### Open a terminal in your project directory and run:
```bash
docker build -t Image_name:latest .
```
This builds the image and tags it as `Image_name:latest`. You can verify the build using `docker images`.

**Note:** If you don't define tag name during docker image creation. You would need to create a tag for the image later.

### Step-3: Tag the Docker Image (Optional, but recommended):
For pushing to a registry, it's useful to have a more descriptive tag. Let's assume your Docker Hub username is `hriyen`:
```bash
docker tag Image_name:latest hriyen/Image_name:v1.0
```
This creates a new tag v1.0 for the image on Docker Hub under your username's repository `Image_name`.

### Managing Images

Remember: Replace placeholders like project names, port numbers, and commands with your specific application details.

- **Listing Images:** Use `docker images` to view all images on your system.
- **Tagging Images:** Create informative tags for your images, use `docker tag <source_image>:<tag> <repository>:<tag>` (e.g., `docker tag my-python-app:latest myrepository/my-python-app:latest`).
- **Removing Images:** Use `docker rmi <image_id>` to remove unwanted images.
- **Pruning Images:** Remove dangling and unused images to free up disk space using `docker image prune`.
- **Inspecting Images:** Get detailed information about a specific image using `docker image inspect <image_name>`.
- **History of Images:** View the history of an image to see its layers and commands using `docker image history <image_name>`.

Additional Useful Commands:

- **Pulling Images:** Download images from a Docker registry using `docker pull <image_name>`.
- **Building Images:** Build an image from a Dockerfile in the current directory using `docker build -t <image_name> .`.
- **Copying Images:** Save an image to a tarball using `docker save <image_name> > image.tar` and load it back using `docker load < image.tar`.
- **Exporting Containers:** Export a container's filesystem as a tar archive using `docker export <container_id> > container.tar`.
- **Importing Containers:** Create a new image from a container's tarball using `docker import container.tar`.
- **Inspecting Containers:** Get detailed information about a specific container using `docker inspect <container_id>`.
- **Container Cleanup:** Remove stopped containers using `docker container prune` to free up resources.


## Pushing images to Docker Registry

Docker Hub is a public registry service for sharing Docker images. Here's a detailed look at pushing and pulling images:

### 01. Pushing to Docker Hub

1. **Create a Docker Hub Account:** If you don't have one already, head to [Docker Hub Signup](https://hub.docker.com/signup) and sign up for a free account.

2. **Login to Docker Hub:** Open your terminal and run the following command, replacing `<username>` with your Docker Hub username:
  ```bash
 docker login -u <username>
 ```
 You'll be prompted to enter your password securely.

3. **Tag Your Image for Pushing:** Let's say you built a Python Flask application image. You can tag it for pushing to your Docker Hub account using:
  ```bash
docker tag my-python-app <username>/my-python-app:latest
 ```
 Replace `<username>` with your username and `my-python-app` with your actual image name. The `:latest` tag refers to the latest version. You can use different tags for specific versions.
 Note: If you have already tagged for a specific image, then you can ingnore this step.

4. **Pushing the Image**
 Now you can push the image to Docker Hub:
 ```bash
 docker push <username>/my-python-app:latest
 ````
 **Congratulations!** The image will be uploaded to your Docker Hub repository.  Its now ready to use it.

**Pulling from Docker Hub (Optional)**
 Anyone with Docker can pull the image from Docker Hub to their local environment.


### 02. Pushing to Azure Container Registry

1. **Create an Azure Account:** If you don't have one already, sign up for an Azure account at [Azure Portal](https://portal.azure.com).

2. **Create an Azure Container Registry (ACR):** In the Azure Portal, navigate to the Azure Container Registry service and create a new registry. Follow the prompts to configure the registry settings and create it.

3. **Login to Azure Container Registry (ACR):** Open your terminal and run the following command, replacing `<registry_name>` with the name of your Azure Container Registry:
   ```bash
   docker login <registry_Login server> 
   ```
   You'll be prompted to authenticate with your Azure account.
   
   **Note:** Please enable AcrPull & AcrPush permision for Azure Container Registry.
    
5. **Tag Your Image for Pushing:** After building your Docker image, tag it with the login server of your Azure Container Registry.
   
   The login server can be found in the Azure Portal under the ACR's properties or Run `az acr show --name <acr_name>` (replace `<acr_name>` with your ACR name).

   ```bash
   docker tag <image_name>:<tag> <acr_login_server>/<image_name>:<tag>
   ```
   Replace <username> with your username and my-python-app with your actual image name. The :latest tag refers to the latest version. You can use different tags for specific versions.
   
   **Note:** If you've already tagged your image for Docker Hub, you'll need to create a separate tag that includes the login server of your Azure Container Registry (ACR) before pushing it there.
   
6. **Pushing the Image to Azure Container Registry (ACR)**

   Replace `<image_name>` with the name of your Docker image, `<tag>` with the desired tag, and `<acr_login_server>` with the login server of your ACR.

   Now, push the tagged image to your Azure Container Registry using the following command:
   ```bash
    docker push <acr_login_server>/<image_name>:<tag>
   ```
  **Congratulations!** This command will upload the image to your ACR repository.
  
You can check your Azure Container Registry in the Azure portal by searching with the registry name. Then navigate to the "`Repositories`" tab to view the available repositories and their contents.
