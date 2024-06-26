FROM rust:1.75.0 AS builder

# create a new empty shell project
RUN USER=root cargo new --bin history
WORKDIR /history

# copy over manifests and source to build image
COPY Cargo.toml ./Cargo.toml

# create fake main.rs and build dependencies
RUN echo "fn main() {}" > ./src/main.rs
RUN apt-get update && apt-get install clang -y
RUN cargo build --release

# copy real src over and make newest modified file
COPY src ./src

RUN touch -a -m ./src/main.rs

# build for release
RUN cargo build --release

# final base
FROM ubuntu:22.04

RUN apt update && apt install wget -y

# copy build artifacts from build stage
COPY --from=builder /history/target/release/history .
ADD https://raw.githubusercontent.com/ethereum/portal-spec-tests/master/tests/mainnet/history/hive/test_data_collection_of_forks_blocks.yaml ./test-data/test_data_collection_of_forks_blocks.yaml

ENV RUST_LOG=debug

ENTRYPOINT ["./history"]
