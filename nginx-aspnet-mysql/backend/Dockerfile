# syntax=docker/dockerfile:1.4

FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:6.0@sha256:fc57fca25ca6b7de2ffc260c145a27eec47f692211c117456f09f15d34f3a9a2 AS base

WORKDIR /src

COPY aspnetapp.csproj ./
RUN ["dotnet", "restore"]

FROM base as builder

COPY . .

CMD ["dotnet", "build", "-c", "-o", "/build"]

FROM builder as dev-envs

RUN <<EOF
apt-get update
apt-get install -y git
EOF

RUN <<EOF
useradd -s /bin/bash -m vscode
groupadd docker
usermod -aG docker vscode
EOF
# install Docker tools (cli, buildx, compose)
COPY --from=gloursdocker/docker / /

CMD ["dotnet", "run"]

FROM builder AS publisher

RUN ["dotnet", "publish", "-c", "Release", "-o", "/build"]

FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/aspnet:6.0

WORKDIR /app
COPY --from=publisher /build .

CMD ["dotnet", "aspnetapp.dll"]
