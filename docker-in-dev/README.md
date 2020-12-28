# Docker in Dev

At the moment, I don't recommend using for developing `lucky` apps unless they are `api-only`. If you are on mac or windows you'll run into some serious issues getting files to sync between the host and the container. There are a bunch of workarounds here (most notably I think [docker-sync](http://docker-sync.io/), please read that site to learn more) but at the end of the day it's a pain when you're dealing with the huge number of files that the node ecosystem relies on.

If it's an api-only app that can still be a pain, but it's much less of an issue if you ignore the shards dir and other little tricks like that.

## Usage

### Setup

This example has two directories which you can put into the root of your lucky project. Note that one of them is called script and thus conflicts with the default `script` dir that comes with Lucky. In this example we're replacing `script/setup` and `script/system_check` with a docker setup that makes them unecessary. But we're keeping the `helpers`. In fact we've added an important function helper called `compose_command` to layer the docker compose files without having to type a bunch at the commandline.

So you might just want to copy `script/docker/` and the `up`, `down`, and `build` scripts from this example into the existing `scripts` directory of your app and then add the `compose_command` function.

The last thing you'll need to do (though I must admit I haven't actually dug enough into this confidently answer why) is change `host` in the `config/watch.yml` to `0.0.0.0`:

```yaml
host: 0.0.0.0
port: 5000
```

### Env Vars

The next step is to customize some of these example files to match your project. I've replaced my project name with `PROJECT_NAME` everywhere here, so you can customize this by doing the reverse.

### Run

With the above done, you should be able to build an image locally with the build script `script/build` it'll get tagged as `PROJECT_NAME:dev` thanks to the way `docker-compose` works. Then you can spin it up with `script/up` and tear it down with `script/down`.

**Note**: The `Dockerfile` here uses the `lucky-crystal` image: https://github.com/luckyframework-docker/lucky-crystal as a base. That means it has crystal and lucky cli baked into the image for development.

## Further Reading

If you're into the above but looking for more details, you might want to check out: https://github.com/KCErb/lucky-hasura-docker. That's a repo that goes into painstaking detail on the why behind some of the things in this example and includes an awesome GraphQL engine called "Hasura".