FROM microsoft/dotnet:2.1.302-sdk AS build
WORKDIR /app
COPY . .
RUN dotnet restore
ENTRYPOINT [ "dotnet", "run" ]