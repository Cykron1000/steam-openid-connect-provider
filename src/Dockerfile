FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build

WORKDIR /src

# Copy the project file to create layer with packages
COPY SteamOpenIdConnectProvider.csproj .
RUN dotnet restore ./SteamOpenIdConnectProvider.csproj

# Copy the rest of the source
COPY . .
RUN dotnet build ./SteamOpenIdConnectProvider.csproj -c Release -o /app

FROM build AS publish
RUN dotnet publish ./SteamOpenIdConnectProvider.csproj -c Release -o /app

FROM mcr.microsoft.com/dotnet/aspnet:9.0

# Security: upgrade OS packages before install (libssl3, gnutls CVE fixes)
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y --no-install-recommends curl && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY --from=publish /app .


ENV ASPNETCORE_URLS "http://*:8080"
EXPOSE 8080

HEALTHCHECK CMD curl --fail http://localhost:8080/health || exit 1
ENTRYPOINT ["dotnet", "SteamOpenIdConnectProvider.dll"]
