# Minimal Executable

The `Dockerfile` here assumes you are using a full app (not api-only) and builds the node and lucky dependencies in different [intermediate stages](https://docs.docker.com/develop/develop-images/multistage-build/). These stages depend only on the dependency files (`yarn.lock`, `shards.yaml` and the like) so they can be cached if the dependencies don't change.

The final stage copies the main app executable `start_server`, the contents of the `public` dir, and the tasks as `run_task` into a clean linux alpine image. Since the entrypoint is `start_server` you can use this as an 'executable image'.

**Note**: This image does not use root as the default user. Instead a `lucky` user is created with UID 1000.

## Usage

First step is to build the image. Here's an example creating a brand-new lucky app `mini-exec` and an image for it tagged as `lucky/mini-exec`.

```
lucky init.custom mini-exec
cp docker-examples/minimal-executable/Dockerfile mini-exec
cd mini-exec
script/setup
docker build -t lucky/mini-exec .
```

There are many ways to make use of this image, so this directory includes one example that uses `docker-compose`. The included `docker-compose.yml` file uses the example names here like `mini-exec` so be sure to update those and then you can run:

```
cp docker-examples/minimal-executable/docker-compose.yml mini-exec
cd mini-exec
docker-compose up
```

and you'll be able to see the app on `localhost:5000`.