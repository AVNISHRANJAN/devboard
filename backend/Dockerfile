# =========================
# Stage 1: Build
# =========================
FROM golang:1.26.8-alpine AS builder

WORKDIR /app

# Install CA certificates and git
RUN apk add --no-cache ca-certificates git

# Copy Go dependency files
COPY go.mod go.sum* ./

# Download dependencies
RUN go mod download

# Copy source code
COPY . .

# Build Go application
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -trimpath -ldflags="-s -w" -o devboard-backend .


# =========================
# Stage 2: Production
# =========================
FROM alpine:3.22

WORKDIR /app

# Install CA certificates for HTTPS/API calls
RUN apk add --no-cache ca-certificates \
    && addgroup -S app \
    && adduser -S -G app -u 10001 app

# Copy compiled binary
COPY --from=builder /app/devboard-backend .

RUN chown app:app /app/devboard-backend

USER 10001:10001

# Backend port
EXPOSE 8080

# Start application
CMD ["./devboard-backend"]
