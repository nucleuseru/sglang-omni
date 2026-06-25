ARG HF_TOKEN

FROM lmsysorg/sglang:dev AS base

ENV HF_TOKEN=${HF_TOKEN}
ENV PORT=8000

# install UCX
RUN git clone https://github.com/openucx/ucx.git \
    && cd ucx \
    && git checkout v1.20.x \
    && ./autogen.sh \
    && ./contrib/configure-release-mt  \
    --enable-shared                    \
    --disable-static                   \
    --disable-doxygen-doc              \
    --enable-optimizations             \
    --enable-cma                       \
    --enable-devel-headers             \
    --with-cuda=/usr/local/cuda        \
    --with-verbs                       \
    --with-dm                          \
    --prefix=/usr/local                \
    && make -j \
    && make -j install-strip \
    && ldconfig

RUN uvx hf download fishaudio/s2-pro

WORKDIR /sglang-omni

RUN uv venv /sglang-omni/.venv -p 3.12

ENV PATH="/sglang-omni/.venv/bin:$PATH"

COPY . .

RUN uv pip install --no-cache-dir .

EXPOSE ${PORT}

CMD ["sgl-omni", "serve", "--model-path", "fishaudio/s2-pro", "--config", "examples/configs/s2pro_tts.yaml", "--port", "$PORT"]
