# ِDocker Management

Here's the process in English for managing Docker, compiling projects, creating Docker images, and pushing them to Docker Hub.

## 1. Installing Docker Desktop

**Download and Install Docker Desktop**: Go to the *[official Docker website](https://www.docker.com/products/docker-desktop)* and download the version suitable for your operating system (Windows or Mac).

**Install Docker Desktop**: Run the installer and follow the installation steps.

**Launch Docker Desktop**: After installation, start Docker Desktop. You may need to restart your system.

## 2. Logging into Docker Hub

**Sign Up for Docker Hub**: Visit the *[Docker Hub website](https://hub.docker.com)* and create an account if you don't already have one.

**Log in to Docker Desktop**: After installing Docker Desktop, log in with your Docker Hub username and password.

## 3. Compiling the Project

Navigate to the Project Directory: Use a terminal or command prompt to go to your project's directory.
Run the Compile Command: To compile the project, use `dotnet build -c release`.

## 4. Building a Docker Image

**Create a Dockerfile**(which is ready): In the root directory of your project, create a file named Dockerfile. This file contains the instructions for building your Docker image. A simple example for a Node.js application is:


```javascript
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 5021

ENV ASPNETCORE_URLS=http://+:5021

USER app
FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG configuration=Release
WORKDIR /src
COPY ["Configuration.API/Configuration.API.csproj", "Configuration.API/"]
RUN dotnet restore "Configuration.API/Configuration.API.csproj"
COPY . .
WORKDIR "/src/Configuration.API"
RUN dotnet build "Configuration.API.csproj" -c $configuration -o /app/build

FROM build AS publish
ARG configuration=Release
RUN dotnet publish "Configuration.API.csproj" -c $configuration -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "BiApp.Configuration.API.dll"]

```

**Build the Image**: Use the following command to build your Docker image:

```javascript
docker build -t your-image-name .
```
Replace **your-image-name** with your desired image name.

**View Images**: After building, you can list your Docker images with:

```javascript
docker images
```

## 5. Running a Container

To run a container from the built image, use:

```javascript
docker run -d -p 8080:80 your-image-name
```

This command runs the container in the background and maps port 8080 on your host to port 80 in the container.

## 6. Tagging the Image

To push an image to Docker Hub, you must tag it with your Docker Hub username. Assuming your Docker Hub username is username and you want to push the image as myapp, use:

```javascript
docker tag your-image-name username/myapp
```

This command creates a new tag for your image.

## 7. Logging in to Docker Hub

If you're not already logged in, use the following command:

```javascript
docker login
```

Enter your Docker Hub username and password when prompted.

## 8. Pushing the Image to Docker Hub

After logging in, push your image with:

```javascript
docker push username/myapp
```

This command uploads your image to your Docker Hub repository. The push process may take some time, depending on the image size and your internet speed.

## 9. Viewing the Image on Docker Hub

Once the push is complete, you can go to your Docker Hub account and see the new image.

### Additional Notes

Version Management: To manage different versions of an image, you can use tags. For example, to tag the image as version 1.0, use:

```javascript
docker tag your-image-name username/myapp:1.0
```
Then push it with 
```javascript
docker push username/myapp:1.0.
```
