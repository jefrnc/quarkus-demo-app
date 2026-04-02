# Quarkus Demo App

Minimal Quarkus REST API designed for container deployment demos on OpenShift and Kubernetes. Used as the demo application in the [OpenShift 4.4 Workshop: Knative, Tekton & Quarkus](https://github.com/jefrnc/openshift-tekton-workshop).

**Watch the workshop:** [YouTube](https://www.youtube.com/watch?v=gs71zQUA_6k&t=1806s)

## Tech Stack

- **Quarkus** 1.4.2 — Supersonic, subatomic Java framework
- **RESTEasy** — JAX-RS REST endpoints
- **GraalVM** — Native image compilation support
- **Java 11**

## Quick Start

### Run in dev mode (live reload)

```bash
./mvnw compile quarkus:dev
```

App available at http://localhost:8080

### Endpoints

| Method | Path | Description |
|---|---|---|
| GET | `/hello` | Returns "hello world!!!" |
| GET | `/` | Quarkus welcome page |

### Run tests

```bash
./mvnw test
```

## Build Options

### JVM mode

```bash
./mvnw package
java -jar target/helloworld-1.0.0-SNAPSHOT-runner.jar
```

### Native mode (GraalVM)

```bash
# With GraalVM installed
./mvnw package -Pnative

# Without GraalVM (uses Docker)
./mvnw package -Pnative -Dquarkus.native.container-build=true

# Run the native binary
./target/helloworld-1.0.0-SNAPSHOT-runner
```

## Deploy to OpenShift

```bash
oc new-project quarkus-demo
oc new-build --binary --name=quarkus-demo -l app=quarkus-demo

# Use native Dockerfile
oc patch bc/quarkus-demo -p '{"spec":{"strategy":{"dockerStrategy":{"dockerfilePath":"src/main/docker/Dockerfile.native"}}}}'

oc start-build quarkus-demo --from-dir=. --follow
oc new-app --image-stream=quarkus-demo:latest
oc expose service quarkus-demo
```

## Deploy to Kubernetes / k3s

```bash
# Build Docker image
docker build -f src/main/docker/Dockerfile.native -t quarkus-demo .

# Run locally
docker run -d -p 8080:8080 quarkus-demo

# Or apply to k3s/k8s
kubectl create deployment quarkus-demo --image=quarkus-demo --port=8080
kubectl expose deployment quarkus-demo --type=NodePort --port=8080
```

## Project Structure

```
quarkus-demo-app/
├── src/
│   ├── main/
│   │   ├── java/com/semperti/
│   │   │   └── ExampleResource.java    # REST endpoint
│   │   ├── docker/
│   │   │   ├── Dockerfile.jvm          # JVM-based container
│   │   │   └── Dockerfile.native       # Native binary container
│   │   └── resources/
│   │       ├── application.properties
│   │       └── META-INF/resources/
│   │           └── index.html          # Welcome page
│   └── test/
│       └── java/com/semperti/
│           ├── ExampleResourceTest.java
│           └── NativeExampleResourceIT.java
├── pom.xml
└── mvnw
```

## Related

- [openshift-tekton-workshop](https://github.com/jefrnc/openshift-tekton-workshop) — Full workshop repo with Tekton pipelines, Knative configs, and deployment guides
- [Quarkus Documentation](https://quarkus.io/)
- [Quarkus Project Generator](https://code.quarkus.io/)

## License

MIT
