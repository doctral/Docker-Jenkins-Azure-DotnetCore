# download the aspnet image to run and deploy the application
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2-stretch-slim AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

# download the sdk to build and test the solution
FROM mcr.microsoft.com/dotnet/core/sdk:2.2-stretch AS build
WORKDIR /src

# copy the solution and projects to directory /src/
COPY ./*.sln ./
COPY ./*/*.csproj ./
RUN for file in $(ls *.csproj); do mkdir -p ./${file%.*}/ && mv $file ./${file%.*}/; done

# restore the dependencies and tools of all projects in the solution under /src
RUN dotnet restore
COPY . .

# -c Release: build the configuration as Release
# -o /app: output the build results to /app
WORKDIR "/src/DotNetApiDocker"
RUN dotnet build "DotNetApiDocker.csproj" -c Release -o /app

# run all tests
FROM build AS test
WORKDIR "/src"
RUN dotnet test

# publish the project with Release  results and output the results to /app
FROM build AS publish
RUN dotnet publish "DotNetApiDocker.csproj" -c Release -o /app

# deploy the projects
FROM base AS final
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "DotNetApiDocker.dll"]
