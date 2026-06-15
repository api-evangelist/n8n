---
title: "Trace n8n workflow and node executions with OpenTelemetry"
url: "https://blog.n8n.io/trace-n8n-workflow-and-node-executions-with-opentelemetry/"
date: "2026-06-08"
author: "Paul Gordon, n8n"
feed_url: "https://blog.n8n.io/rss/"
---
n8n now supports OpenTelemetry natively, allowing every workflow execution to be emitted as a trace, so n8n shows up inside the same observability stack your team already uses for the rest of production. There is no sidecar to run and no patching to maintain, and because everything is built on
