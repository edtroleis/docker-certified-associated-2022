# Build muilt-stage
# docker image build -t test-go-multi:1.0.0 .
# docker container run test-go-multi:1.0.0

FROM golang AS builder

WORKDIR /app
ADD ./test-go.go /app
RUN go build test-go.go


FROM alpine

WORKDIR /go
COPY --from=builder /app/test-go /go

ENTRYPOINT ./test-go
