FROM golang:1.22 AS base

WORKDIR /app

COPY go-web-app/go.mod ./

RUN go mod download

COPY go-web-app/. .

RUN go build -o main .

# Stage 2: Create a minimal distroless image

FROM gcr.io/distroless/base

COPY --from=base /app/main .

COPY --from=base /app/static ./static

EXPOSE 8080

CMD ["./main"]