## Without build multi-stage
# docker image build -t test-go:1.0.0 .
# docker container run test-go:1.0.0

FROM golang

WORKDIR /app
ADD ./test-go.go /app
RUN go build test-go.go

ENTRYPOINT ./test-go
