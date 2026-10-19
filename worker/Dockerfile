# Build stage
FROM --platform=${BUILDPLATFORM} mcr.microsoft.com/dotnet/sdk:7.0 AS build

ARG TARGETPLATFORM
ARG TARGETARCH
ARG BUILDPLATFORM

RUN echo "I am running on $BUILDPLATFORM, building for $TARGETPLATFORM"

WORKDIR /source

COPY *.csproj ./
RUN dotnet restore -a $TARGETARCH

COPY . ./
RUN dotnet publish -c Release -o /app -a $TARGETARCH --self-contained false --no-restore

# Production runtime stage
FROM mcr.microsoft.com/dotnet/runtime:7.0

WORKDIR /app

# Create a dedicated non-root user
RUN useradd --create-home --shell /usr/sbin/nologin appuser

COPY --from=build --chown=appuser:appuser /app ./

# Run the worker without root privileges
USER appuser

ENTRYPOINT ["dotnet", "Worker.dll"]