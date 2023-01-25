FROM python:3.10-slim-bullseye
RUN apt-get update && apt-get install -y zsh 
SHELL ["/bin/zsh", "-c", "-o", "pipefail"]

RUN if ! getent passwd app; then groupadd -g 1000 app \
    && useradd -u 1000 -g 1000 -d /home/app -m -s /bin/zsh app; fi \
    && echo app:app | chpasswd \
    && echo 'app ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers \
    && mkdir -p /etc/sudoers.d \
    && echo 'app ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers.d/app \
    && chmod 0440 /etc/sudoers.d/app \
    && apt-get autoremove \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/* 

USER app
WORKDIR /home/app

RUN touch /home/app/.zshrc \
   && echo 'PS1="$ "' >> /home/app/.zshrc \
   && echo 'export PATH=/home/app/.local/bin:$PATH' >> /home/app/.zshrc


RUN \
    source /home/app/.zshrc \
    && mkdir -p /home/app/app \
    && mkdir -p /home/app/app/tests \
    && mkdir -p /home/app/app/docs \
    && mkdir -p /home/app/data

ENV PATH="/home/app/local/bin:${PATH}"
ENV COLORTERM=truecolor
ENV PYTHONDONTWRITEBYTECODE=1


WORKDIR /home/app/app
RUN python -m pip install --user --upgrade pip
COPY --chown=app requirements.txt .
COPY --chown=app requirements.dev.txt .
RUN python -m pip install --user -r requirements.txt
RUN python -m pip install --user -r requirements.dev.txt
COPY --chown=app app/ /home/app/app/app
COPY --chown=app tests/ /home/app/app/tests/
