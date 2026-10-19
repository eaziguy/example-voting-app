# Use a specific Node.js version and Debian release
FROM node:22-bookworm-slim

# Install curl for health checks and tini for process management
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl tini && \
    rm -rf /var/lib/apt/lists/*

# Create a dedicated non-root user
RUN useradd --create-home --shell /usr/sbin/nologin appuser

WORKDIR /usr/local/app

# Install nodemon for local development
RUN npm install -g nodemon

# Install application dependencies
COPY package*.json ./

RUN npm ci && \
    npm cache clean --force && \
    mv /usr/local/app/node_modules /node_modules

# Copy application files and assign ownership
COPY --chown=appuser:appuser . .

# Configure the application
ENV PORT=80
EXPOSE 80

# Run as a non-root user
USER appuser

ENTRYPOINT ["/usr/bin/tini", "--"]
CMD ["node", "server.js"]