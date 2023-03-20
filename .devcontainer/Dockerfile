FROM node:18-bullseye

ARG USER
ARG WORKSPACES
ARG HOME

# パッケージのインストール
# ------------------------------------------------------------------------------
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends sudo less locales

# ユーザー設定
# ------------------------------------------------------------------------------
RUN userdel -r node && useradd -m ${USER} -u 1000 -s /bin/bash
# ローカル開発環境のため許容している
RUN echo "${USER} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# 日本語化対応
# ------------------------------------------------------------------------------
RUN sed -i -e 's/# \(ja_JP.UTF-8\)/\1/' /etc/locale.gen \
    && locale-gen \
    && update-locale LANG=ja_JP.UTF-8
ENV LANG=ja_JP.UTF-8

# 永続化
# ------------------------------------------------------------------------------
RUN SNIPPET="export PROMPT_COMMAND='history -a' && export HISTFILE=/commandhistory/.bash_history" \
    && mkdir -p /commandhistory ${HOME}/.vscode-server/extensions \
    && touch /commandhistory/.bash_history \
    && chown -R ${USER}:${USER} /commandhistory ${HOME}/.vscode-server \
    && echo ${SNIPPET} >> "${HOME}/.bashrc"

# プロンプトのカスタマイズ
# ------------------------------------------------------------------------------
RUN sh -c "$(curl -fsSL https://starship.rs/install.sh)" -- --yes \
    && echo 'eval "$(starship init bash)"' >> ${HOME}/.bashrc

USER ${USER}
RUN mkdir -p ${HOME}/.config \
    && { \
    echo '[time]'; \
    echo 'disabled = false'; \
    echo 'utc_time_offset = "9"'; \
    echo 'format = "🕘 [$time]($style) "'; \
    echo ''; \
    echo '[container]'; \
    echo 'format = "[$symbol]($style) "'; \
    echo ''; \
    echo '[nodejs]'; \
    echo 'format = "via [⚡ $version](bold green) "'; \
    } >> ${HOME}/.config/starship.toml

# Slack CLI インストール
# ------------------------------------------------------------------------------
USER ${USER}
RUN curl -fsSL https://downloads.slack-edge.com/slack-cli/install.sh | bash
