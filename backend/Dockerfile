# =========================
# Stage 1: Build
# =========================
FROM golang:1.25-alpine AS builder

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
    go build -o devboard-backend .


# =========================
# Stage 2: Production
# =========================
FROM alpine:latest

WORKDIR /app

# Install CA certificates for HTTPS/API calls
RUN apk add --no-cache ca-certificates

# Copy compiled binary
COPY --from=builder /app/devboard-backend .

# Backend port
EXPOSE 8080

# Start application
CMD ["./devboard-backend"]
