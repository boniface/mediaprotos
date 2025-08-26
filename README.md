# Media Proto Central Repository

This repository contains the Media Protobuf definitions (`.proto` files) for common domain entities. The goal is to maintain consistency and reuse across multiple services implemented in Rust, Scala, and TypeScript.

## Initial  Repository Structure

```
mediaprotos
├── protos
│   ├── geo.proto
│   ├── feed.proto
│   ├── website.proto
│   ├── website_link.proto
│   ├── content.proto
│   ├── country.proto
│   └── region.proto
├── README.md
├── LICENSE
└── .gitignore
```

## Getting Started


```bash
# HTTPS (recommended)
git clone https://github.com/boniface/mediaprotos.git

# SSH (if you have SSH keys configured)
git clone git@github.com:boniface/mediaprotos.git
```

---

## Integration Guide

### Rust Integration (using `prost`)

#### Step 1: Add as Git Submodule

```bash
git submodule add git@github.com:boniface/mediaprotos.git
```

#### Step 2: Add dependencies to `Cargo.toml`

```toml
[dependencies]
prost = "0.14"
prost-types = "0.14"
tokio = { version = "1.47", features = ["macros", "rt-multi-thread"] }

[build-dependencies]
prost-build = "0.14"
```

#### Step 3: Automate Proto Compilation (`build.rs`)

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

#### Step 4: Usage Example

```rust
// Generated code will be available as:
use mediaprotos::location::v1::{Country, Region, GeoCoordinate};
use mediaprotos::feed::v1::Feed;
use mediaprotos::website::v1::{Website, WebsiteLink};
use mediaprotos::content::v1::{Content, Image};

fn main() {
    let country = Country {
        id: "zm".to_string(),
        code: "ZM".to_string(),
        name: "ZAMBIA".to_string(),
        ..Default::default()
    };
    
    println!("Country: {}", country.name);
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
libraryDependencies ++= Seq(
  "com.thesamet.scalapb" %% "scalapb-runtime" % "0.11.19"
)

// Configure ScalaPB
Compile / PB.targets := Seq(
  scalapb.gen() -> (Compile / sourceManaged).value / "scalapb"
)

Compile / PB.protoSources := Seq(file("mediaprotos/protos"))
```

#### Step 3: Add ScalaPB plugin to `project/plugins.sbt`

```scala
addSbtPlugin("com.thesamet" % "sbt-protoc" % "1.0.6")
libraryDependencies += "com.thesamet.scalapb" %% "compilerplugin" % "0.11.19"
```

#### Step 4: Compile and Usage

```bash
sbt compile
```

```scala
// Usage example
import mediaprotos.location.v1.{Country, Region, GeoCoordinate}
import mediaprotos.feed.v1.Feed
import mediaprotos.website.v1.{Website, WebsiteLink}
import mediaprotos.content.v1.{Content, Image}

object Main extends App {
  val country = Country(
    id = "zm",
    code = "ZM", 
    name = "ZAMBIA"
  )
  
  println(s"Country: ${country.name}")
}
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
  --ts_proto_opt=outputServices=grpc-js \
  -I ./mediaprotos/protos \
  ./mediaprotos/protos/*.proto
```

#### Step 4: Usage Example

```typescript
// Import generated types
import { Country, Region, GeoCoordinate } from './generated/mediaprotos/location/v1/country';
import { Feed } from './generated/mediaprotos/feed/v1/feed';
import { Website, WebsiteLink } from './generated/mediaprotos/website/v1/website';
import { Content, Image } from './generated/mediaprotos/content/v1/content';

// Usage
const country: Country = {
  id: 'zm',
  code: 'ZM',
  name: 'ZAMBIA',
  officialName: 'Repblic of Zambia',
  regionId: 'Central Africa',
  // ... other fields
};

console.log(`Country: ${country.name}`);
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

This project is licensed under the Apache License 2.0. See [LICENSE](LICENSE) for details.

---

## Contributions

Contributions to this repository are welcomed. Please follow these guidelines:

1. Fork the repository.
2. Create your feature branch (`git checkout -b feature/YourFeature`).
3. Commit your changes (`git commit -am 'Add YourFeature'`).
4. Push to the branch (`git push origin feature/YourFeature`).
5. Create a new Pull Request.
