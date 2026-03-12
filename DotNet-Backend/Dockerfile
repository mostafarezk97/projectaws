FROM mcr.microsoft.com/dotnet/aspnet:8.0

WORKDIR /app

# Copy published output from CI pipeline
COPY publish/ .

# Expose API port
EXPOSE 8080

# Start the API
ENTRYPOINT ["dotnet", "API.dll"]