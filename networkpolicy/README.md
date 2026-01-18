# Securing Databases in Kubernetes Using Network Policies

Securing databases inside a Kubernetes cluster is a **critical real-world DevOps challenge**.  
By default, Kubernetes networking is **flat**, meaning any Pod can communicate with any other Pod across namespaces.

This document explains **why database access must be restricted**, how **Network Policies** solve the problem, and how to design a **secure production-ready setup**.

---

## Problem Statement

Databases such as Redis, MongoDB, or PostgreSQL are often deployed inside Kubernetes.  
Even if these databases run in a separate namespace, **any Pod in the cluster can still access them** unless network restrictions are explicitly defined.

This creates a serious security risk:
- A compromised Pod can scan internal services
- Attackers can directly connect to databases
- Sensitive data can be exposed without authentication

Namespaces provide **logical separation**, not network isolation.

---

## Real-World Use Case

We assume the following setup:

### Namespaces
- **secure-namespace**
  - Database Pod (Redis / MongoDB)
  - Authorized application Pods

- **sandbox (or default namespace)**
  - Developer Pods
  - Test or experimental workloads

### Security Requirement
- Only authorized application Pods should access the database
- Pods from other namespaces must be denied
- Even if a Pod is compromised, database access must remain protected

---

## Attack Scenario Without Network Policies

A database is running in `secure-namespace` without any Network Policy.  
An attacker gains access to a Pod in another namespace and deploys a simple container.

From that Pod:
- The attacker retrieves the database Pod IP
- Installs a database client (e.g., redis-cli)
- Connects directly to the database using the Pod IP

Because Kubernetes allows unrestricted Pod-to-Pod communication by default, the connection succeeds.

This happens even though the database is deployed in a “secure” namespace.

---

## Why Network Policies Are Required

Network Policies allow you to explicitly define:
- Which Pods can talk to other Pods
- Which namespaces are allowed
- What ports and protocols are permitted

Once a Network Policy is applied:
- Traffic is **denied by default**
- Only explicitly allowed connections are permitted

This enforces **zero-trust networking inside the cluster**.

---

## Network Policy Basics

Kubernetes Network Policies are divided into two categories:

### Ingress
Controls **incoming traffic** to a Pod  
Used to restrict access to databases and backend services

### Egress
Controls **outgoing traffic** from a Pod  
Used to prevent Pods from accessing unsecured internal or external services

For database security, **Ingress policies are the most critical**.

---

## Restricting Database Access

The recommended approach is:
- Apply Network Policies to database Pods
- Allow traffic only from Pods with specific labels
- Block all other namespaces and Pods

This can be achieved using:
- Pod selectors
- Namespace selectors
- Label-based access control

Avoid relying on Pod IP addresses, as they are dynamic.

---

## Important Prerequisite

Network Policies require a **CNI plugin that supports them**.

They may not work out of the box in:
- minikube
- kind

They work reliably in:
- Managed Kubernetes (AKS, EKS, GKE)
- OpenShift
- Clusters using Calico, Cilium, or Weave

Always verify Network Policy support before testing.

---

## Key Takeaways

- Kubernetes networking is open by default
- Namespaces alone do not provide security
- Databases must be isolated using Network Policies
- Network Policies enforce zero-trust inside the cluster
- A compromised Pod should never mean compromised data

---

## Reference

Official Kubernetes documentation:  
https://kubernetes.io/docs/concepts/services-networking/network-policies/
