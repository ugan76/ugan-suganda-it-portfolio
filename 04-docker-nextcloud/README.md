# 04 — Docker and Nextcloud Deployment

**Category:** Application Deployment / System Administration  
**Status:** Documentation template — evidence to be added

## 1. Project Overview

Document the deployment and operation of Nextcloud using Docker containers
in a lab environment.

## 2. Objectives

- Install and verify Docker.
- Deploy the application using the method actually used.
- Understand container lifecycle and persistent data.
- Access and test the Nextcloud web interface.
- Record service health and troubleshooting.

## 3. Lab Environment

| Component | Actual value |
|---|---|
| Host OS | [TODO] |
| Docker version | [TODO] |
| Compose version, if used | [TODO] |
| Nextcloud image/version | [TODO] |
| Database / storage arrangement | [TODO] |
| Network / ports | [TODO — sanitize] |

## 4. Architecture

Add a diagram to `architecture/`:

`![Deployment architecture](architecture/deployment-architecture.png)`

## 5. Implementation

[TODO: record installation, deployment, volumes, network, and application setup.
Do not include secrets or real `.env` files.]

## 6. Testing and Results

| Test | Method | Actual result |
|---|---|---|
| Container running | `docker ps` | [TODO] |
| Application reachable | Browser | [TODO] |
| Login works | Lab account | [TODO] |
| File upload/download | Test file | [TODO] |
| Restart behavior | Restart container | [TODO] |

## 7. Evidence

Add screenshots of container status, application dashboard, and a test file
operation. Use a dummy account and non-sensitive files.

## 8. Troubleshooting and Lessons Learned

[TODO]

## 9. Security Notes

Never commit `.env`, passwords, database credentials, access tokens, or
production data. Use `.env.example` with placeholder values only if needed.
