

# Support

## Building Binaries

### Checking OS Architecture

Before building the binary, you need to determine the target platform's OS architecture. Use the following commands:

#### On Linux/Ubuntu:
```bash
# Check machine hardware architecture
uname -m

# Alternative command
arch

# Check Debian package architecture (for Ubuntu/Debian)
dpkg --print-architecture

# Detailed CPU architecture information
lscpu | grep Architecture
```

#### Common Architecture Mappings:
- `x86_64` or `amd64` → Use `GOARCH=amd64`
- `aarch64` or `arm64` → Use `GOARCH=arm64`
- `armv7l` → Use `GOARCH=arm`
- `i386` or `i686` → Use `GOARCH=386`

#### On macOS:
```bash
uname -m
# Intel Macs: x86_64
# Apple Silicon Macs: arm64
```

#### On Windows:
```powershell
$env:PROCESSOR_ARCHITECTURE
# or
wmic os get osarchitecture
```

### Building the Binary

This project uses SLSA GoReleaser configurations located in `.slsa-goreleaser/` directory. The build process creates statically linked binaries with no CGO dependencies.

#### Build for Linux ARM64 (recommended for modern cloud deployments):
```bash
# Create binaries directory
mkdir -p binaries

# Build using SLSA configuration settings
GO111MODULE=on CGO_ENABLED=0 GOOS=linux GOARCH=arm64 go build -o binaries/aks-mcp-linux-arm64 ./cmd/aks-mcp
```

#### Build for Linux AMD64 (x86_64):
```bash
GO111MODULE=on CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o binaries/aks-mcp-linux-amd64 ./cmd/aks-mcp
```

#### Build for macOS:
```bash
# For Apple Silicon Macs
GO111MODULE=on CGO_ENABLED=0 GOOS=darwin GOARCH=arm64 go build -o binaries/aks-mcp-darwin-arm64 ./cmd/aks-mcp

# For Intel Macs
GO111MODULE=on CGO_ENABLED=0 GOOS=darwin GOARCH=amd64 go build -o binaries/aks-mcp-darwin-amd64 ./cmd/aks-mcp
```

#### Build for Windows:
```bash
# For 64-bit Windows
GO111MODULE=on CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build -o binaries/aks-mcp-windows-amd64.exe ./cmd/aks-mcp
```

### Build Configuration Explanation

The build uses these environment variables:
- `GO111MODULE=on`: Ensures Go modules are used for dependency management
- `CGO_ENABLED=0`: Creates a statically linked binary with no external dependencies
- `GOOS`: Target operating system (linux, darwin, windows)
- `GOARCH`: Target architecture (amd64, arm64, arm, 386)

### SLSA GoReleaser Reference

The `.slsa-goreleaser/linux-arm64.yml` file contains the reference configuration:
```yaml
version: 1
env:
  - GO111MODULE=on
  - CGO_ENABLED=0
goos: linux
goarch: arm64
main: ./cmd/aks-mcp
binary: aks-mcp-{{ .Os }}-{{ .Arch }}
```

You can create similar configuration files for other target platforms by modifying the `goos` and `goarch` values.

### Testing the Binary

After building, test the binary:
```bash
# Check if binary was created
ls -la binaries/

# Test the binary
./binaries/aks-mcp-linux-arm64 --help
```

### Container Deployment

The statically linked binaries are ideal for container deployment as they have no external dependencies. You can create minimal container images using scratch or distroless base images.

## How to file issues and get help  

This project uses GitHub Issues to track bugs and feature requests. Please search the existing 
issues before filing new issues to avoid duplicates.  For new issues, file your bug or 
feature request as a new Issue.

For help and questions about using this project, please check the documentation above for building binaries, or file an issue in the GitHub Issues section.

## Microsoft Support Policy  

Support for this AKS MCP project is limited to the resources listed above.
