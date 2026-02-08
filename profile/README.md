# EnvCrypt

**Secure, end-to-end encrypted environment variable management for modern teams.**

EnvCrypt is an open-source solution designed to replace insecure methods of sharing secrets (like Slack, email, or unencrypted text files) with a zero-knowledge architecture. It ensures your sensitive data never leaves your machine in plaintext, providing a secure and efficient way to manage environment variables across your entire development lifecycle.

## Use Cases

### 1. Secure Team Collaboration
Eliminate the risk of sharing `.env` files over insecure channels. With EnvCrypt, you can onboard new developers instantly. They simply pull the encrypted environment variables for the project they are working on, decrypted locally using their own keys.

### 2. CI/CD Secret Injection
Securely inject secrets into your CI/CD pipelines (GitHub Actions, GitLab CI, etc.) using **Service Roles**. These are machine identities with restricted access that can retrieve secrets at runtime, preventing sensitive data from being exposed in your repository or build logs.

### 3. Production Secret Management
Manage secrets for production environments with confidence. EnvCrypt provides immutable versioning for every change, allowing you to instantly rollback to a previous state if a deployment goes wrong.

### 4. Microservices Configuration
Centralize configuration management for distributed systems. Ensure each microservice has access to the exact secrets it needs, scoped by environment (dev, staging, prod) and project.

## Benefits

*   **Zero-Knowledge Architecture**: Your secrets are encrypted on your device using AES-256-GCM before they ever reach the server. The server acts as a blind storage layer and never sees your raw data or encryption keys.
*   **Developer-First Experience**: A lightning-fast CLI (`envcrypt`) integrates seamlessly into your terminal workflow. No need to switch context to a web dashboard to manage variables.
*   **Self-Hostable & Sovereign**: Run the backend server on your own infrastructure. You maintain full control over your data, access logs, and encryption keys.
*   **Immutable History**: Every `push` creates a new, immutable version of your secrets. Audit changes easily and revert instantly if needed.
*   **Granular Access Control**: Implement the Principle of Least Privilege with sophisticated Role-Based Access Control (RBAC). Grant access only to the specific projects and environments a user or service needs.

---

## Project Components

EnvCrypt consists of two main components: the **CLI** for developers and the **Backend Server** (Self-Hostable ESS) for data storage and coordination.

### 1. EnvCrypt CLI
The command-line interface is the heart of the developer experience. It handles all local encryption, decryption, and key management.

**Key Features:**
-   **Local Encryption**: Uses X25519 for key exchange and AES-256-GCM for secret encryption.
-   **Cross-Platform**: Binaries available for Linux, macOS, and Windows.
-   **Service Role Identity**: dedicated commands to manage machine users for CI/CD.
-   **Smart Context**: Auto-detects git repositories and environments to simplify commands.

### 2. EnvCrypt Backend (Self-Hostable ESS)
The backend server acts as the External Secrets Service (ESS). It is designed to be self-hosted, giving your organization complete control over your secret management infrastructure.

**Key Features:**
-   **Blind Storage**: Stores only encrypted blobs and wrapped keys.
-   **User & Project Management**: Handles user registration, authentication (via public keys), and project metadata.
-   **Access Enforcement**: Validates permissions before allowing access to encrypted data blobs.
-   **PostgreSQL Backed**: Uses robust, standard database technology for reliability.
-   **Docker Ready**: Easily deployable via Docker containers for quick setup in any environment.

## Architecture

EnvCrypt uses a **hybrid cryptosystem** to ensure security and performance:
1.  **Symmetric Encryption**: Environment variables are encrypted with a per-project AES-256 key (Project Master Key or PMK).
2.  **Key Wrapping**: The PMK is encrypted ("wrapped") for each user using their public X25519 key.
3.  **Local Decryption**: The private key needed to unwrap the PMK never leaves the user's device.
4.  **Authenticated Storage**: All API requests are signed. The server validates identity but cannot decrypt the payload.

## Getting Started

### Install the CLI
Start by installing the localized client on your machine.

**Automated Install:**
```bash
curl -fsSL https://raw.githubusercontent.com/envcrypts/envcrypt-cli/main/install.sh | bash
```

**Build from Source:**
If you have Go installed:
```bash
go install github.com/envcrypts/envcrypt-cli@latest
```

### Self-Host the Server
To run your own ESS instance:

1.  Clone the server repository: `git clone https://github.com/envcrypts/envcrypt`
2.  Run with Docker Compose: `docker-compose up -d`
3.  Configure your CLI to point to your new server:
    Set the server URL in your local configuration (~/.config/envcrypt/config.yaml)
    
