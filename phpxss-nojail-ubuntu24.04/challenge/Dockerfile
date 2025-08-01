# Debian 12
# PHP 8.4.5
# Apache 2.4.62

FROM docker.io/library/php@sha256:abce8fe7e3390e96d3ac52dff8e6f5ff9507f4a7bee2b18f11404b74d7efec66

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    python3 python3-pip curl unzip wget util-linux \
    fonts-liberation libasound2 libatk-bridge2.0-0 procps \
    libnss3 lsb-release xdg-utils libxss1 libdbus-glib-1-2 \
    libcairo2 libcups2 libgbm1 libgtk-3-0 libpango-1.0-0 \
    libu2f-udev libvulkan1 libxkbcommon-x11-0 xvfb && apt-get clean

RUN CHROME_SETUP=google-chrome.deb && \
    wget -q -O $CHROME_SETUP "https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb" && \
    dpkg -i $CHROME_SETUP && \
    apt-get install -y -f && \
    rm $CHROME_SETUP
RUN CHROMEDRIVER_VERSION=$(google-chrome --version | sed "s/Google Chrome//" | xargs) && wget -q -O chromedriver_linux64.zip https://storage.googleapis.com/chrome-for-testing-public/$CHROMEDRIVER_VERSION/linux64/chromedriver-linux64.zip && \
    unzip chromedriver_linux64.zip && mv chromedriver-linux64/chromedriver /usr/bin/ && \
    chmod +x /usr/bin/chromedriver && \
    rm chromedriver_linux64.zip && rm -r chromedriver-linux64

COPY --from=ghcr.io/astral-sh/uv:0.7.2 /uv /uvx /bin/
RUN uv venv /venv
ENV PATH="/venv/bin:${PATH}"
RUN uv pip install selenium urllib3 python-decouple requests bs4 pyvirtualdisplay

# Copy challenge required files
COPY config/php.ini $PHP_INI_DIR/php.ini
COPY web /var/www/html
COPY bot /bot
COPY flag.txt /flag.txt
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
RUN touch /tmp/paths && chmod 777 /tmp/paths

SHELL ["/bin/sh", "-c"]
CMD ["/entrypoint.sh"]
