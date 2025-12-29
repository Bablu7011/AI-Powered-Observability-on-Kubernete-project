

```markdown
# 🚀 AI-Powered Observability on Kubernetes using OpenTelemetry & Honeycomb

![Architecture Diagram](images/10.jpg)

## 📌 Project Overview

Modern cloud-native applications are built using microservices, where a single user request passes through many services. In such systems, debugging issues like slowness, errors, or failures becomes very difficult using traditional monitoring tools.

This project demonstrates how to build a **fully observable Kubernetes environment** using:
* **OpenTelemetry** for collecting telemetry data.
* **Honeycomb** for analyzing traces, metrics, and logs.
* **Honeycomb MCP (Model Context Protocol)** for AI-based natural language debugging.
* **Kubernetes (Minikube)** for running a real multi-service application locally.

With this setup, instead of manually navigating dashboards, we can ask questions in plain English like:
> *"Which service was slow in the last 30 minutes?"*
> *"Why is checkout slow today?"*

---

## 🧠 Real-Life Problem This Project Solves

| ❌ Traditional Debugging Problems | ✅ Solution with This Project |
| :--- | :--- |
| Too many scattered dashboards | Centralized telemetry (metrics, logs, traces) |
| Hard-to-write query languages | **AI-assisted debugging** using natural language |
| No clear "why" behind failures | Faster root-cause analysis |
| Difficult to trace across services | Request-level distributed tracing |

---

## 🏗️ Architecture

**Data Flow:**
1.  **User Request** → OpenTelemetry Demo App (Microservices)
2.  **Telemetry Collection** → OpenTelemetry Collector
3.  **Data Ingestion** → Honeycomb Observability Platform
4.  **AI Analysis** → Honeycomb MCP Server
5.  **Interaction** → VS Code Copilot Chat (Natural Language Queries)

---

## 🧰 Tools & Technologies Used

* ![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=flat&logo=kubernetes&logoColor=white) **Minikube**
* ![Helm](https://img.shields.io/badge/HELM-0F1689?style=flat&logo=helm&logoColor=white) **Helm Charts**
* ![OpenTelemetry](https://img.shields.io/badge/OpenTelemetry-000000?style=flat&logo=opentelemetry&logoColor=white) **OpenTelemetry Collector**
* ![Honeycomb](https://img.shields.io/badge/Honeycomb.io-FFC53D?style=flat&logo=honeycomb&logoColor=black) **Honeycomb & MCP**
* ![VS Code](https://img.shields.io/badge/Visual%20Studio%20Code-0078d7.svg?style=flat&logo=visual-studio-code&logoColor=white) **VS Code + Copilot Chat**

---

## 📁 Project Structure

```bash
Observability-Project/
│
├── values.yaml          # OpenTelemetry Collector configuration (Honeycomb exporter)
├── README.md            # Project documentation
└── images/              # Screenshots used in README

```

---

## ⚙️ Step-by-Step Implementation

### 1️⃣ Create Local Kubernetes Cluster

We use Minikube to create a local cluster with enough resources to run the microservices demo.

```bash
minikube start --cpus=4 --memory=6g --disk-size=40g

```

*Verifying the cluster status:*

### 2️⃣ Create Honeycomb Account & Get API Key

1. Go to [ui.honeycomb.io](https://ui.honeycomb.io).
2. Create an account and a new Environment.
3. Generate a **Team API Key**.

*Honeycomb Account & MCP Server Connection:*

### 3️⃣ Configure OpenTelemetry Collector

Create a `values.yaml` file to configure the OpenTelemetry Collector to export data to Honeycomb.

```yaml
opentelemetry-collector:
  enabled: true
  config:
    receivers:
      otlp:
        protocols:
          http: {}
          grpc: {}
    processors:
      batch: {}
    exporters:
      otlphttp/honeycomb:
        endpoint: [https://api.honeycomb.io](https://api.honeycomb.io)
        headers:
          x-honeycomb-team: "<YOUR_API_KEY>"
          x-honeycomb-dataset: "otel-demo"
    service:
      pipelines:
        traces:
          receivers: [otlp]
          processors: [batch]
          exporters: [otlphttp/honeycomb]
        metrics:
          receivers: [otlp]
          processors: [batch]
          exporters: [otlphttp/honeycomb]

```

### 4️⃣ Deploy OpenTelemetry Demo Application

Deploy the demo using Helm. This installs over 15 microservices including Kafka, Postgres, and the Otel Collector.

```bash
helm repo add open-telemetry [https://open-telemetry.github.io/opentelemetry-helm-charts](https://open-telemetry.github.io/opentelemetry-helm-charts)
helm repo update
kubectl create namespace otel-demo

helm upgrade --install otel-demo open-telemetry/opentelemetry-demo \
  -n otel-demo -f values.yaml

```

*Helm Deployment Success:*

### 5️⃣ Verify Deployment

Check the pods to ensure they are starting up.

```bash
kubectl get pods -n otel-demo

```

*Pods Initializing:*

*All Pods Running:*

### 6️⃣ Access the Demo Application

Port-forward the frontend service to access the web store.

```bash
kubectl port-forward -n otel-demo svc/frontend-proxy 8080:8080

```

*Port Forwarding:*

Open your browser to `http://localhost:8080` to see the application.

*Webstore UI:*

*Locust Load Generator Stats:*

### 7️⃣ Integrate Honeycomb MCP with VS Code

To enable AI debugging, add the MCP server configuration to VS Code.

1. Edit your VS Code settings JSON or MCP config.
2. Add the Honeycomb MCP server details:

```json
{
  "servers": {
    "honeycomb-server": {
      "url": "[https://mcp.honeycomb.io/mcp](https://mcp.honeycomb.io/mcp)",
      "type": "http"
    }
  }
}

```

*VS Code MCP Integration & Chat:*

---

## 🤖 Example Natural Language Queries (MCP)

With the MCP server connected, you can ask Copilot questions directly in VS Code:

* *"Which services handled the most requests in the last hour?"*
* *"Show the slowest endpoints in the last 30 minutes"*
* *"Why is checkout slow today?"*
* *"List all datasets in my Honeycomb project"*

---

## 🎯 What I Learned

* **Observability vs. Monitoring:** Understanding the difference between tracking "unknown unknowns" vs "known knowns".
* **OpenTelemetry:** How to instrument a Kubernetes cluster and export telemetry.
* **Distributed Tracing:** Visualizing requests as they travel through Kafka, Redis, and Go/Java/Python microservices.
* **AI Debugging:** Using the Model Context Protocol (MCP) to bridge the gap between hard data and natural language questions.

---

## 🏁 Conclusion

This project demonstrates the future of debugging. By combining **OpenTelemetry** for standards-based data collection and **Honeycomb MCP** for AI analysis, we can significantly reduce the Mean Time To Resolve (MTTR) production issues.

**🔖 Keywords**
`Kubernetes` `OpenTelemetry` `Honeycomb` `Observability` `MCP` `AI Debugging` `Microservices` `Helm`

```

```