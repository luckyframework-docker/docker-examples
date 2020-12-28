FROM luckyframeworkdocker/lucky-crystal:l0.25.0c0.35.1-alpine

# Don't default to root
RUN addgroup -g 1000 -S lucky && \
    adduser -u 1000 -S lucky -G lucky
USER lucky

# Copy minimum to install shards and npm packages in image
COPY --chown=lucky:lucky shard.yml /home/lucky/app/
WORKDIR /home/lucky/app

RUN shards install

CMD ["lucky", "watch"]
