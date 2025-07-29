# Media Proto Central Repository

This repository contains the Media Protobuf definitions (`.proto` files) for common domain entities. The goal is to maintain consistency and reuse across multiple services implemented in Rust, Scala, and TypeScript.

## Sample Repository Structure

```
mediaprotos
├── protos
│   ├── user.proto
│   └── address.proto
├── README.md
├── LICENSE
└── .gitignore
```

## Access Management

This repository is private. Ensure you have access via GitHub using one of the following methods:

### SSH Access (Recommended)

* Add your SSH public key to your GitHub account (Settings → SSH keys).
* Clone using SSH:

```bash
git clone git@github.com:boniface/mediaprotos.git
```

### HTTPS Access

* Generate a Personal Access Token (PAT) with repository permissions in GitHub (Settings → Developer settings → Personal access tokens).
* Clone using HTTPS:

```bash
git clone https://github.com/boniface/mediaprotos.git
```

---

## Integration Guide

### Rust Integration (using `prost`)

#### Step 1: Add as Git Submodule

```bash
git submodule add git@github.com:boniface/mediaprotos.git
```

#### Step 2: Automate Proto Compilation (`build.rs`)

```rust
use std::fs;
use std::path::{Path, PathBuf};

fn collect_proto_files(proto_dir: &Path) -> Vec<PathBuf> {
    fs::read_dir(proto_dir)
        .expect("Cannot read proto directory")
        .filter_map(|entry| {
            let path = entry.expect("Dir entry").path();
            if path.extension().and_then(|s| s.to_str()) == Some("proto") {
                Some(path)
            } else {
                None
            }
        })
        .collect()
}

fn main() {
    let proto_dir = Path::new("mediaprotos/protos");
    let proto_files = collect_proto_files(proto_dir);

    prost_build::Config::new()
        .compile_protos(&proto_files, &[proto_dir])
        .expect("Failed to compile protos");
}
```

---

### Scala Integration (using ScalaPB)

#### Step 1: Add as Git Submodule

```bash
git submodule add git@github.com:boniface/mediaprotos.git
```

#### Step 2: Configure `build.sbt`

```scala
Compile / PB.protoSources := Seq(file("mediaprotos/protos"))

libraryDependencies ++= Seq(
  "com.thesamet.scalapb" %% "scalapb-runtime" % "0.11.17" % "protobuf"
)
```

Run compilation:

```bash
sbt compile
```

---

### TypeScript Integration (using `ts-proto`)

#### Step 1: Add as Git Submodule

```bash
git submodule add git@github.com:boniface/mediaprotos.git
```

#### Step 2: Install dependencies

```bash
npm install ts-proto @grpc/grpc-js protobufjs
```

#### Step 3: Generate TypeScript definitions

```bash
protoc \
  --plugin=./node_modules/.bin/protoc-gen-ts_proto \
  --ts_proto_out=./generated \
  --ts_proto_opt=esModuleInterop=true \
  -I ./proto-central-repo/protos \
  ./mediaprotos/protos/*.proto
```

---

## Updating Protos

### Making Changes

1. Clone and checkout your proto-central-repo.
2. Make changes to `.proto` files.
3. Commit and push changes:

```bash
git commit -am "Update User proto structure"
git push origin main
```

### Updating in Consumer Projects

```bash
git submodule update --remote --merge
```

Regenerate code following the specific language instructions provided above.

---

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

---

## Contributions

Contributions to this repository are welcomed. Please follow these guidelines:

1. Fork the repository.
2. Create your feature branch (`git checkout -b feature/YourFeature`).
3. Commit your changes (`git commit -am 'Add YourFeature'`).
4. Push to the branch (`git push origin feature/YourFeature`).
5. Create a new Pull Request.
