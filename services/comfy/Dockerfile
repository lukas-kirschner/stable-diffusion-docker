FROM pytorch/pytorch:2.3.0-cuda12.1-cudnn8-runtime

ENV DEBIAN_FRONTEND=noninteractive PIP_PREFER_BINARY=1

RUN apt-get update && apt-get install -y git python3-opencv libgl1-mesa-glx python3-numba && apt-get clean

ENV ROOT=/stable-diffusion
RUN --mount=type=cache,target=/root/.cache/pip \
  git clone https://github.com/comfyanonymous/ComfyUI.git ${ROOT} && \
  cd ${ROOT} && \
  git checkout master && \
  git reset --hard 601b4b63e1c0af2deef8630449497b9e84aa00f8 && \
  pip install -r requirements.txt

WORKDIR ${ROOT}

ARG BRANCH=master SHA=601b4b63e1c0af2deef8630449497b9e84aa00f8
RUN --mount=type=cache,target=/root/.cache/pip \
  git fetch && \
  git checkout ${BRANCH} && \
  git reset --hard ${SHA} && \
  pip install -r requirements.txt

# Install common dependencies for custom nodes
RUN pip install matplotlib opencv-python GitPython smmap matrix-client gitdb scikit-image onnxruntime-gpu dynamicprompts Pillow importlib_metadata huggingface_hub scipy filelock einops pyyaml python-dateutil mediapipe svglib fvcore yapf omegaconf ftfy addict numba

# add info
COPY . /docker/
RUN chmod u+x /docker/entrypoint.sh && cp /docker/extra_model_paths.yaml ${ROOT}

ENV NVIDIA_VISIBLE_DEVICES=all PYTHONPATH="${PYTHONPATH}:${PWD}" CLI_ARGS=""
EXPOSE 7860
ENTRYPOINT ["/docker/entrypoint.sh"]
CMD python -u main.py --listen --port 7860 ${CLI_ARGS}
