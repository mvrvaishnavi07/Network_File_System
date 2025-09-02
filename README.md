# Network File System (NFS) Implementation

## Overview
This project implements a fully functional Network File System (NFS) with a client-server architecture. The system consists of three major components:
- **Clients**: Request and interact with the NFS, performing operations like reading, writing, deleting, streaming, and listing files.
- **Naming Server (NM)**: Acts as a central coordinator, managing the directory structure, locating files, and facilitating communication between clients and storage servers.
- **Storage Servers (SS)**: Handle physical file storage and execute operations requested by the NM and clients.

## Implemented Functionalities

### 1. Naming and Storage Servers
#### 1.1 Initialization
- **Naming Server Initialization**: The NM is initialized as the central entity managing file paths and storage servers.
- **Dynamic IP and Port Allocation**: IP and port are not hardcoded, allowing the NM to inform storage servers and clients dynamically.
- **Storage Server Initialization**: Each SS registers with the NM by providing:
  - IP address
  - NM connection port
  - Client connection port
  - List of accessible paths
- **Multiple Storage Servers**: Additional SS instances can be initialized dynamically and registered with the NM.
- **Client Requests Handling**: The NM starts accepting client requests once all SSs are registered.

#### 1.2 Storage Server Functionalities
- **Dynamic Storage Server Addition**: New SSs can register with the NM at runtime.
- **Commands Issued by NM to SSs**:
  - Create empty files/directories
  - Delete files/directories
  - Copy files/directories between SSs
- **Client Interactions via SSs**:
  - Read files
  - Write files
  - Retrieve file size and permissions
  - Stream audio files in binary format for playback
- **Efficient Client-SS Communication**: NM provides the correct SS details to clients for direct communication.

#### 1.3 Naming Server Functionalities
- **Storage Server Data Management**: NM maintains a registry of all SSs and their accessible paths.
- **Client Task Feedback**: NM provides real-time acknowledgments and status updates to clients.

### 2. Client Functionalities
#### 2.1 File Operations
- **Path Resolution**: NM determines the correct SS for a requested file.
- **Supported Commands**:
  - `READ <path>`: Retrieve file contents.
  - `WRITE <path>`: Modify file contents.
  - `INFO <path>`: Retrieve file metadata (size, permissions, timestamps).
  - `STREAM <path>`: Stream audio files directly.
  - `CREATE <path> <name>`: Create new files or directories.
  - `DELETE <path>`: Remove files or directories.
  - `COPY <source> <dest>`: Copy files/directories between SSs.
  - `LIST`: Retrieve all accessible paths.

#### 2.2 Copying Files Between SSs
- NM validates source and destination paths and coordinates the transfer between SSs.
- Upon successful completion, NM acknowledges the client.

### 3. Additional Features
#### 3.1 Asynchronous and Synchronous Writing
- **Asynchronous Writing**: Large writes are buffered and periodically flushed to disk.
- **Immediate Acknowledgment**: SS acknowledges the client immediately upon receiving data.
- **Failure Handling**: If SS fails during an asynchronous write, NM informs the client.
- **Priority Writes**: Clients can specify synchronous writes for critical operations.

#### 3.2 Multiple Clients
- **Concurrent Client Support**: NM can handle multiple simultaneous client requests.
- **Efficient Processing**: NM does not block while processing operations.
- **Concurrent Reads**: Multiple clients can read the same file simultaneously.
- **Exclusive Writes**: Only one client can write to a file at a time.

#### 3.3 Error Handling
- **Defined Error Codes**: Clear and descriptive error codes are returned for issues such as:
  - File not found
  - File being modified by another client
  - Storage Server unavailable

#### 3.4 Optimized Search in NM
- **Efficient Search Structures**: Implemented Tries and Hashmaps for faster file lookups.
- **LRU Caching**: Frequently accessed paths are cached to improve response times.

#### 3.5 Data Backup and Recovery
- **Failure Detection**: NM detects SS failures and reroutes client requests to backups.
- **Replication Strategy**: Files are replicated across at least two SSs (if available).
- **Asynchronous Duplication**: Writes are asynchronously propagated to backup SSs.

#### 3.6 Redundancy (Bonus)
- **Storage Server Recovery**: When an SS comes back online, it resynchronizes with its backup data.

#### 3.7 Logging and Bookkeeping
- **Request Logging**: NM logs all client and SS interactions for debugging and monitoring.
- **IP & Port Tracking**: Every communication event is recorded with IP and port details.

## Conclusion
This implementation provides a robust and scalable Network File System, supporting dynamic storage servers, efficient client interactions, fault tolerance, and optimized search operations. The system ensures reliability through replication, asynchronous writes, and intelligent error handling, making it a practical solution for distributed file storage needs.
