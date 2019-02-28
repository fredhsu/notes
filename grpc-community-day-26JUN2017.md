26-JUN-2017
grpc-istio community day

hsaliak@google/twitter

# GRPC where we are and where we are going
## Intro
Borg -> k8s
stubby -> grpc (next gen stubby internally as well)
grpc runs everwhere, IoT, Mobile

## vs existing options
* easy and universal as REST but better performance
* What about rEST/CRUD?
    * Separate policies from waht API infra can do
    * If you want REST, force stateless RESTful APIs
    * If you want CRUD, enforce single service definition with CRUD
* Others
    * Thrift - no active development
    * Finagle - single lang

## Provides strrict service contracts - protobuf
* Strictly typed contracts
* BAckward / foward compabitility of apis
    * never delete a field
    * never reuse a field tag
    * server doesn't need to track which clients are using which fields
* GPC design guide for designing protobufs

## Extensible with plugins
* Monitoring/tracing
    * promoethus, zipkin, opentracing
    * uses interceptor
* service discvoery
    * etcd, consul, zookeeper as controller to grpc-lb
* auth & security
* Proxies
    * nghttp2, haproxy, traefik
    * advanced: envoy, linkerd, google LB

## Roadmap
* Moving from core framework to tools for building, running, operating services
* Stats, tracing, LB, retries, service config, web support, health checking, rate limiting
### Service config
* Policies where server tells client what they should do
    * per service parameters, eg. LB policy
    * defaults for methods
        * Max msg size
        * deadline
* Discovery by DNS
### Retries
* Pitfalls of districuted computing - networks can fail
* Need resiliance for network failures
* Today if a connection is broken before rpc starts, reconnect automatically with exponential backoff
* retry support brings the ability ot automatically retry failed rpcs based on service config
### Load Balancing
* Proxy based
* Client side
* gRPC-LB - lookaside LB - clients and servers send load reports to LB
    * Allows centralization of policies and reduce complexity

# gRPC best practices
Eric Anderson
Doug fawley
Abhishek Kumar - a11r

* Error handling
    * Language specific - Go - reponse given back with an error
    * Streaming will give errors back for messages sent
    * Canonical status code - what you should use for error handling, description is for debugging
    * Server always finishes by sending back a status code/message
        * status code high level with app developer in mind
* Debugging
    * CLI tool in the grpc/grpc repo - like curl
    * Reflection services
    * Suggestion - wireshark plugin, postman plugin
    * Suggestion - html doc generator
* Sharing proto files
    * Single central repo for all the proto files
    * Make sure there is a canonical version of the proto file
* TEsting
    * In Go, setup local host client and server and test
    * typically in the same application
    * Use *real* fakes for the data built by the service owner that will be served instead of Mocks
* When to use streaming
    * pub/sub
    * When we don't know how many response will be returned
    * don't use it if you don't need it, many other parts could be missing

