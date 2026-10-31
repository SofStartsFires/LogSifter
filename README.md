# LogSifter

**LogSifter** is a full-stack log ingestion, automated severity classification, and interactive terminal debugging console built with **React**, **Vite**, **Node.js**, **Express**, **Multer**, and **Axios**. 

It allows developers and site reliability engineers to ingest raw, unstructured application or server logs—either by pasting text streams or uploading `.log` / `.txt` files—and immediately triage incidents using regex-driven line parsing, real-time diagnostic aggregation, and multi-dimensional filtering.

---

## Features

<img width="1105" height="975" alt="image" src="https://github.com/user-attachments/assets/d3e982cc-3004-4392-867f-8edae5c5ea73" />

- **Dual Log Ingestion**
  - **Paste Raw Buffer:** Paste unstructured server, container, or application logs directly into the workbench.
  - **In-Memory File Upload:** Drag and drop or browse for `.log`, `.txt`, `.out`, `.err`, `.json`, or `.csv` files (up to 15 MB) processed directly in memory via `multer.memoryStorage()` without writing temporary files to disk.
  - **Built-In Incident Presets:** Includes three realistic multi-service log streams (*Production API & DB Pool Crash*, *Kubernetes Pod OOM & Queue Stall*, and *Nominal Cluster Boot & TLS Audit*) for instant testing.

- **Automated Regex Parsing & Classification**
  - Scans log streams sequentially line-by-line (`/\r?\n/`).
  - Extracts timestamps (ISO-8601, syslog, and bracketed formats) and service/component identifiers (e.g., `[postgres-pool]`, `[order-worker]`).
  - Categorizes lines into **`ERROR`**, **`WARN`**, and **`INFO`** based on prioritized keyword signatures (`FATAL`, `CRITICAL`, `Exception`, `Traceback`, `OOMKilled`, `WARNING`, `DEPRECATED`, `TIMEOUT`, etc.).
  - Automatically associates indented stack trace frames (`at ...` or `Caused by:...`) with preceding `ERROR` entries.

- **Real-Time Diagnostic Metrics**
  - Calculates **Total Parsed Lines**, **Total Errors** (with error rate `%`), **Total Warnings** (with warning rate `%`), and **Informational Lines**.
  - Aggregates a live breakdown of detected trigger signatures (e.g., `Exception`, `StackTrace`, `TIMEOUT`, `FATAL`) for one-click signature isolation.

- **Interactive Dark-Mode Terminal & Triage Controls**
  - **Instant Severity Filtering:** Toggle between `ALL`, `ERROR`, `WARN`, and `INFO` streams.
  - **Service & Signature Filtering:** Narrow logs down to a single microservice or matched trigger keyword.
  - **Live Substring & Regex Search:** Filter log payloads in real time with plain text or regular expressions (`.*`).
  - **Error Stepper (`Jump Error`):** Cycle forward and backward through `ERROR` lines in large log files.
  - **Line Inspector & Export:** Click any log row to inspect its parsed metadata and raw payload, copy individual lines or the filtered stream to the clipboard, or export results as `.json` or `.log`.

---

## Tech Stack

| Layer | Technology | Purpose |
| :--- | :--- | :--- |
| **Frontend** | React 19, TypeScript, Vite | Single-page application (SPA), state management, and interactive filtering |
| **Styling** | Tailwind CSS, Lucide React | Dark-mode terminal UI, tabular numerals (`JetBrains Mono` & `Plus Jakarta Sans`) |
| **HTTP Client** | Axios | Asynchronous `POST` requests for JSON text buffers and `multipart/form-data` uploads |
| **Backend** | Node.js, Express | REST API server, regex log parser, and metric aggregation engine |
| **File Handling** | Multer | In-memory `multipart/form-data` file ingestion (`multer.memoryStorage()`) |

---

## Classification Rules

LogSifter evaluates each non-empty line against an ordered set of regular expression patterns:

1. **`ERROR` Triggers (Highest Priority)**
   - Keywords: `FATAL`, `CRITICAL`, `PANIC`, `ERROR`, `ERR`, `SEVERE`, `*Exception`, `Traceback`, `SEGFAULT`, `Segmentation fault`, `OOMKilled`, `OutOfMemoryError`, `ECONNREFUSED`, `ETIMEDOUT`, `EADDRINUSE`, `FAILED`, `FAILURE`
   - Context: Indented stack frames (`^\s+at\s+...`) or `Caused by:` lines immediately following an `ERROR` line.
2. **`WARN` Triggers**
   - Keywords: `WARNING`, `WARN`, `DEPRECATED`, `DeprecationWarning`, `TIMEOUT`, `timed out`, `RETRY`, `Retrying`, `SLOW`, `High latency`, `Pool exhausted`, `RATE_LIMIT`, `429 Too Many Requests`
3. **`INFO` Triggers (Default / Nominal)**
   - Keywords: `INFO`, `DEBUG`, `TRACE`, `NOTICE`, or standard operational log lines without error/warning indicators.

---

## Getting Started

### Prerequisites

- **Node.js** (v18+ recommended)
- **npm** (v9+ recommended)

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/<your-username>/logsifter.git
   cd logsifter
