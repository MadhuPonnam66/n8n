# ===============================
# Stage 1: Build n8n from source n8n cloned repo
# ===============================
FROM node:22-alpine AS builder

# setting  working directory
WORKDIR /usr/src/app

# Installing build dependencies
RUN apk add --no-cache git python3 make g++ tzdata


# Enabling corepack to install with pnpm instead of npm

RUN corepack enable



# Copying dependency files

COPY ./pnpm-lock.yaml ./pnpm-workspace.yaml ./package.json ./patches ./



# Installing dependencies using pnpm

RUN pnpm install --frozen-lockfile

# Copying source code into the container
COPY . .

# Build all packages
RUN pnpm run build

# ===============================
# Stage 2: Create runtime image
# ===============================
FROM node:22-alpine

# Create non-root user for security
RUN addgroup -S n8n && adduser -S n8n -G n8n

# Setting  work directory
WORKDIR /home/node/app

# Copy built files from builder
COPY --from=builder /usr/src/app ./

# Fixing  permissions
RUN chown -R n8n:n8n /home/node/app

USER n8n

# Expose n8n's default web port
EXPOSE 5678

# Persist workflow and credentials
VOLUME ["/home/node/.n8n"]

# Default command to start n8n
ENTRYPOINT ["node", "packages/cli/bin/n8n"]
CMD ["start"]

