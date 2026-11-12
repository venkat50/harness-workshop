# --- Stage 1: Build the Go binary ---
FROM golang:1.27-alpine AS builder

# Set the working directory inside the container
WORKDIR /app

# Copy dependency files first to leverage Docker caching layers
COPY go.mod go.sum ./
RUN go mod download

# Copy the rest of the source code
COPY . .

# Build a statically linked binary for Linux Alpine/Scratch compatibility
# RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags="-w -s" -o my-app main.go
RUN go build -o my-app main.go

# --- Stage 2: Final minimal runtime image ---
FROM alpine:3.20

# Create a non-root user for security best practices
RUN adduser -D -u 10001 appuser

WORKDIR /home/appuser

# Copy the compiled binary from the builder stage
COPY --from=builder /app/my-app .

# Change ownership to the non-root user
RUN chown -R appuser:appuser /home/appuser

# Switch to the non-root user
USER appuser

# Expose the application port
EXPOSE 8080

# Execute the application
ENTRYPOINT ["./my-app"]
