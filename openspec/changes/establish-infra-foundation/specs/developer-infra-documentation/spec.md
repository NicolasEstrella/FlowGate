## ADDED Requirements

### Requirement: Infrastructure documentation shall define bootstrap and reset flows
The infrastructure documentation SHALL describe how to start, inspect, stop, and fully reset the local environment from a clean workspace.

#### Scenario: Contributor follows bootstrap guide
- **WHEN** a new contributor reads the infrastructure README
- **THEN** they can start the full local environment without relying on undocumented steps

#### Scenario: Contributor resets local state
- **WHEN** a contributor needs to recreate the environment from scratch
- **THEN** the README explains the exact reset command path and its effect on persisted data

### Requirement: Development configuration contract shall be documented
The local platform SHALL define the development environment variables, default credentials, and service URLs required by backend and portal contributors.

#### Scenario: Contributor configures local dependencies
- **WHEN** a developer needs connection details for the local services
- **THEN** the repository provides a single documented source for those values

#### Scenario: Troubleshooting starts from documented endpoints
- **WHEN** a contributor diagnoses a local environment problem
- **THEN** the documentation includes the relevant service endpoints and inspection commands needed for first-line troubleshooting