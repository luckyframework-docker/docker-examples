# STAGE 1: Use node env to build node dependencies
FROM node:alpine as node_build
WORKDIR /tmp_build

# Install node packages in image
COPY package.json .
COPY yarn.lock .
RUN yarn install --no-progress --frozen-lockfile

# Compile assets with webpack
COPY webpack.mix.js .
COPY src ./src
RUN yarn prod

# STAGE 2: Use crystal env to build crystal dependencies
FROM crystallang/crystal:0.35.1-alpine as lucky_build
ENV SKIP_LUCKY_TASK_PRECOMPILATION="1"
WORKDIR /tmp_build
COPY shard.* ./
RUN  shards install --production
COPY . .
COPY --from=node_build /tmp_build/public/mix-manifest.json public/mix-manifest.json
RUN crystal build --static src/start_server.cr
RUN crystal build --static tasks.cr -o run_task

# FINAL: actual image with app binary and static assets
FROM alpine
RUN addgroup -g 1000 -S lucky && \
    adduser -u 1000 -S lucky -G lucky
WORKDIR /home/lucky/app

COPY --chown=lucky:lucky --from=node_build /tmp_build/public public
COPY --chown=lucky:lucky --from=lucky_build /tmp_build/start_server start_server
COPY --chown=lucky:lucky --from=lucky_build /tmp_build/run_task run_task

RUN chown -R lucky /home/lucky
USER lucky

CMD ["/home/lucky/app/start_server"]
