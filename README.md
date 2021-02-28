## GitHub Actions

### Prerequisites:

Install [Visual Studio Code](https://code.visualstudio.com/)

Install [.NET 5.0](https://dotnet.microsoft.com/download/dotnet/5.0)

Install [Git SCM](https://git-scm.com/downloads)


### Loose Agenda:

Set up continuous integration


### Step by Step

#### Setup playground

Create a GitHub repository for today's exercise
Clone that repository to a local directory
Open a terminal to that directory


#### Generate a WebAPI Project

Within the terminal instance, run ```dotnet new webapi -n webapi -o .``` 
We now have our code. Since we're going to check in this code let's generate a gitignore with ```dotnet new gitignore```
Verify that our code builds locally by running ```dotnet build```
Now go ahead and push that code up to GitHub

```
git add .
git commit -m "Initial commit"
git push origin main

```


#### Create a GitHub Action

Open today's repository on GitHub and click the Actions tab.
At the top section click ```set up a workflow yourself```
Adjust the file to the following:

```
name: CI
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  workflow_dispatch:
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Build 
        run: |
          dotnet build

```

At the top right click ```Start commit``` then fill in the modal and click ```Commit new file``` 

Click the Actions tab again and note that you have a running workflow! 


#### Dockerize The Build

Back on our developer environment, let's create a Dockerfile next to the .csproj and enter the following code:

```
FROM mcr.microsoft.com/dotnet/sdk:5.0-buster-slim AS build-env
WORKDIR /app
COPY . ./
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/aspnet:5.0-buster-slim
WORKDIR /app
COPY --from=build-env /app/out .
ENTRYPOINT ["dotnet", "webapi.dll"]

```

Let's pull the latest code with ```git pull``` and let's open the main.yml file in .github/workflows.

Let's change ```dotnet build``` to ```docker build .```

Now let's save everything and commit it to GitHub.

```
git add .
git commit -m "Dockerizing our build"
git push origin main

```

Back in the actions tab of our GitHub repository we should see another build was started.

**Note that now the GitHub workflow definition file is independent of the coding language. So long as we containerize our application, we can generically use our new workflow action.**


### Additional Resources

- [Docker Hub Dotnet SDK](https://hub.docker.com/_/microsoft-dotnet-sdk)
- [Docker Hub Dotnet ASP.NET](https://hub.docker.com/_/microsoft-dotnet-aspnet)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
