ARG baseImage="gaeelbradshaw/base"
FROM $baseImage

ARG UNAME=runner
ARG UID=1000
ARG GID=1000

RUN getent group $GID || groupadd -g $GID $UNAME

# Hub dependencies
RUN apt-get -q update \
 && apt-get -q install -y --no-install-recommends --allow-downgrades zenity \
 && apt-get clean

# Download & extract AppImage
RUN wget --no-verbose -O /tmp/UnityHub.AppImage "https://public-cdn.cloud.unity3d.com/hub/prod/UnityHub.AppImage" \
 && chmod +x /tmp/UnityHub.AppImage \
 && cd /tmp \
 && /tmp/UnityHub.AppImage --appimage-extract \
 && cp -R /tmp/squashfs-root/* / \
 && rm -rf /tmp/squashfs-root /tmp/UnityHub.AppImage \
 && mkdir -p "$UNITY_PATH" \
 && mv /AppRun /opt/unity/UnityHub

# Alias to "unity-hub" with default params
RUN echo '#!/bin/bash\nxvfb-run -ae /dev/stdout /opt/unity/UnityHub --no-sandbox --headless "$@"' > /usr/bin/unity-hub \
 && chmod +x /usr/bin/unity-hub

# Accept as root
RUN mkdir -p "/root/.config/Unity Hub" \
 && touch "/root/.config/Unity Hub/eulaAccepted"

# Configure
RUN mkdir -p "${UNITY_PATH}/editors" \
 && unity-hub install-path --set "${UNITY_PATH}/editors/" \
 && find /tmp -mindepth 1 -delete

RUN chown $UID:$GID -R /opt/unity/

RUN bash -c 'mkdir -p /github/{home,workflow,workspace}' && chown $UID:$GID -R /github/
RUN getent group $GID || groupadd -g $GID $UNAME
RUN id -u $UID &>/dev/null || useradd -m -u $UID -g $GID -s /bin/bash -d /github/home $UNAME

USER $UID:$GID

# Accept as runner
RUN mkdir -p "/github/home/.config/Unity Hub" \
 && touch "/github/home/.config/Unity Hub/eulaAccepted"
