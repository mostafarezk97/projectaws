FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final
WORKDIR /app

ENV ASPNETCORE_HTTP_PORTS=8080

COPY ./publish ./

EXPOSE 8080
ENTRYPOINT ["dotnet", "API.dll"]
