FROM golang

WORKDIR /app
ADD ./test-go.go /app
RUN go build test-go.go

ENTRYPOINT ./test-go

# docker image build -t test-go:1.0.0 .