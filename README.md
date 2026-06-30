# Remnant

> A turnkey, open-source, and free Windows backup solution designed for the home.

## Overview

Remnant is a robust, transparent, and least-privilege Windows backup application. It provides an intuitive, wizard-driven experience to easily back up your local disks, Gmail, Google Drive, and Google Photos. You can route these backups to external drives, network drives, a separate Google Drive folder, or Google Cloud Storage. Remnant is entirely free and open-source; the only potential cost is the storage you consume if you choose to use Google Cloud Storage.

Under the hood, Remnant is orchestrated via a Node.js/TypeScript main service and a Vue.js web UI. It utilizes a native Tauri/WebView2 shell to provide a proper application window, system tray presence, and toast notifications without requiring a standalone browser. To ensure maximum security, operations requiring Windows API access—like Volume Shadow Copy and NTFS Change Journals—are offloaded to a separate, elevated Rust service.

## Core Features

* **Turnkey Setup Wizards:** Easily configure complex backup routes (e.g., Disk/Gmail/Photos to Network/Cloud) without touching a command line.
* **Completely Free:** Open-source and free to use at home. Your only expense is your chosen cloud storage provider.
* **Transparent Storage (No Vendor Lock-in):** Backups are saved as standard `.zip` archives alongside plain JSON manifest files (`.manifest.json`). This guarantees your data is never trapped in a proprietary format, providing the ultimate peace of mind that your files are always accessible with standard tools if necessary.
* **Advanced Incremental Detection:** Instead of relying on often-unreliable file modification timestamps, Remnant reads the NTFS USN (Update Sequence Number) Change Journal. This perfectly detects changed, added, or deleted files, even if they have older timestamps.
* **Consistent Reads via VSS:** All local file reads use the Windows Volume Shadow Copy Service (VSS). This guarantees consistent snapshots and ensures no partially written files are captured during backup.
* **Database-Free Operation:** Configuration, state, and job histories are entirely JSON-based. Config files maintain their own copy-on-change revision history for automated self-healing against corruption.

## Architecture

Remnant's architecture is divided into three primary components that communicate via HTTP, SSE, and Windows named pipes:

1. **`Remnant-service` (Node.js/TypeScript):** The main orchestration engine running in Session 0 under a restricted virtual service account (`NT SERVICE\Remnant-service`). It handles all source backup jobs, API communication with Google, file transfers, and serves the Vue.js UI over a local HTTP port.
2. **`Remnant-elevated` (Rust):** Runs with elevated privileges (`SYSTEM` or Administrator). It exclusively handles OS-level privileged operations (VSS snapshots, USN Journal reads, config ACL repair). It communicates with the main service via a secured named pipe using JSON-RPC 2.0.
3. **Native Shell (Tauri/WebView2):** A lightweight Rust application running in the user's desktop session. It hosts the Vue.js UI, manages the system tray icon, and relays Windows toast notifications via a Server-Sent Events (SSE) connection to the main service.

## Repository Structure

The project is built as an npm workspace monorepo, combining TypeScript packages and Rust crates.

```text
/
├── service/     # Remnant-service (Node.js / TypeScript)
├── elevated/    # Remnant-elevated (Rust, runs as SYSTEM)
├── app/         # Tauri native shell (Rust + WebView2)
├── ui/          # Vue.js SPA (served by Remnant-service)
├── shared/      # Shared TypeScript types, API contracts, and Zod schemas
└── scripts/     # PowerShell install (sc.exe) and management scripts

```

## Technology Stack

* **Backend:** Node.js, Fastify, Zod, `@google-cloud/storage`, `googleapis`
* **Elevated Service:** Rust, `windows` crate, `jsonrpc-core`
* **Frontend & Shell:** Tauri, Vue.js, Pinia, Vite
* **Build Systems:** Turborepo (TS orchestration), Cargo (Rust compilation)
