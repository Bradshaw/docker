ARG baseImage="unityci/base"
FROM $baseImage

# Hub dependencies
RUN apt-get -q update \
 && apt-get -q install -y --no-install-recommends --allow-downgrades zenity \
 && apt-get clean


USER $UID:$GID

# Download & extract AppImage
RUN wget --no-verbose -O /tmp/UnityHub.AppImage "https://public-cdn.cloud.unity3d.com/hub/prod/UnityHub.AppImage" \
 && chmod +x /tmp/UnityHub.AppImage \
 && cd /tmp \
 && /tmp/UnityHub.AppImage --appimage-extract \
 && mkdir -p "$APPDIR" \
 && cp -R /tmp/squashfs-root/* $APPDIR \
 && rm -rf /tmp/squashfs-root /tmp/UnityHub.AppImage \
 && mkdir -p "$UNITY_PATH" \
 && mv $APPDIR/AppRun $UNITY_PATH/UnityHub

# Alias to "unity-hub" with default params
RUN echo '#!/bin/bash\nAPPIMAGE_SILENT_INSTALL=true APPDIR='$APPDIR' xvfb-run -ae /dev/stdout '$UNITY_PATH'/UnityHub --no-sandbox --headless "$@"' > $RUNNER_HOME/bin/unity-hub \
 && chmod +x $RUNNER_HOME/bin/unity-hub

# Configure
RUN mkdir -p "${UNITY_PATH}/editors" \
 && unity-hub install-path --set "${UNITY_PATH}/editors/" \
 && find /tmp -mindepth 1 -delete 2> /dev/null
