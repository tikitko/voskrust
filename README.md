# voskrust

conveniently use [vosk-api](https://github.com/alphacep/vosk-api) as a rust library.

This project uses a Dockerfile to build a `libvosk.a` static library (which is just vosk-api + all of its dependencies), and then uses that static library when linking with the rust executable.

To create the `libvosk.a` you can do the following:

```sh
# this will take a while...
docker build -t voskbuilder .
id=$(docker create voskbuilder)
docker cp $id:/opt/vosk-api/src/copydir/libvosk.a ./
docker rm -v $id
```

Now you will have `libvosk.a` in the root directory of this repository.

(Alternatively, you can run `./getlibvoskfromdocker.sh` which performs the above in one step)

The `build.rs` build script simply adds the root of this repository to the library search path, and in our `src/raw.rs` file, we add `#[link(name = "vosk")]` which tells rust to look for `libvosk.a`

You can try a minimal example:

```sh
cargo build --example simpletest
./target/debug/example/simpletest
```

simpletest just uses the `vosk_set_log_level` function. If it builds and runs successfully, then the other functions should work as well.
