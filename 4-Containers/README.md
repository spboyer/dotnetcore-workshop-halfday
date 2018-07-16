# Containers for .NET Core

## Console Application Example

Sample Code in [dotnetcore-docker-sample](dotnetcore-docker-sample)

```console
dotnet new console -o dotnetcore-docker-sample
```

Add a new file **Dockerfile**

```Docker
FROM microsoft/dotnet:2.1.302-sdk AS build
WORKDIR /app
COPY . .
RUN dotnet restore
ENTRYPOINT [ "dotnet", "run" ]
```

Build the **dev** tagged version of the Docker image

```console
docker build -t dotnetcore-docker-sample:dev .
```

Add a new file **Dockerfile.production**

```Docker
FROM microsoft/dotnet:2.1.302-sdk AS build
WORKDIR /app

# copy csproj and restore as distinct layers
WORKDIR /app
COPY *.csproj .
RUN dotnet restore

# copy and publish app and libraries
WORKDIR /app
COPY . .
WORKDIR /app
RUN dotnet publish -c Release -o out

FROM microsoft/dotnet:2.1.2-runtime AS runtime
WORKDIR /app
COPY --from=build /app/out ./
ENTRYPOINT ["dotnet", "dotnetcore-docker-sample.dll"]
```

Create a multi-stage **production** tagged version of the Docker image

```console
docker build -t dotnetcore-docker-sample:prod -f Dockerfile.production .
```

Use `docker images` command to inspect the images created using each command.

```console
docker images | grep dotnetcore-docker-sample

dotnetcore-docker-sample    prod        80efb69090fa        24 seconds ago       180MB
dotnetcore-docker-sample    latest      4a5c523e7f62        About a minute ago   1.7GB
```

Run either image using `docker run <image>:<tag>`

## Resources

* [Docs - Building Docker Images for .NET Core Applications](https://docs.microsoft.com/dotnet/core/docker/building-net-docker-images?WT.mc_id=workshop-github-shboyer)
* [.NET Core Docker Samples](https://github.com/dotnet/dotnet-docker)