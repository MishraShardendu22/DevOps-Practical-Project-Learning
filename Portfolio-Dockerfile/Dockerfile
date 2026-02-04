FROM ubuntu:22.04

RUN apt-get update && apt-get install -y \
    curl \
    git \
    build-essential \
    python3 \
    python3-pip \
    supervisor

RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - \
    && apt-get install -y nodejs

RUN npm install -g pnpm

RUN curl -OL https://go.dev/dl/go1.24.4.linux-amd64.tar.gz \
    && tar -C /usr/local -xzf go1.24.4.linux-amd64.tar.gz

ENV PATH="/usr/local/go/bin:${PATH}"
ENV CI=true

WORKDIR /app

COPY . .

# Install and build MishraShardendu monorepo
WORKDIR /app/MishraShardendu
RUN pnpm install
RUN pnpm build

# Install and build MishraShardendu22-Backend-BlogWebsite
WORKDIR /app/MishraShardendu22-Backend-BlogWebsite
RUN pnpm install
RUN pnpm build

# Build Go backend for PersonalWebsite
WORKDIR /app/MishraShardendu22-Backend-PersonalWebsite
RUN go mod tidy
RUN go build -o server

# Install and build MS22 Next.js app
WORKDIR /app/MS22
RUN pnpm install
RUN pnpm build

WORKDIR /app

RUN mkdir -p /etc/supervisor/conf.d
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

EXPOSE 5173
EXPOSE 5174
EXPOSE 3000
EXPOSE 3001
EXPOSE 5000

CMD ["/usr/bin/supervisord", "-n"]
