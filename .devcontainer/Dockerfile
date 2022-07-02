#-------------------------------------------------------------------------------------------------------------
# Flutter Dev Container - by XanderThunder.
# Version v1.0 22-07-02
# Licensed under the MIT License.
# See https://github.com/XanderThunder/dev-container-flutter/blob/master/LICENSE for license information.
#-------------------------------------------------------------------------------------------------------------

FROM ubuntu:22.04

#Locale
ENV LANG en_US.UTF-8

#
# Android SDK
# https://developer.android.com/studio#downloads
ENV ANDROID_SDK_TOOLS_VERSION 8512546
ENV ANDROID_PLATFORM_VERSION 33
ENV ANDROID_BUILD_TOOLS_VERSION 33.0.0
ENV ANDROID_HOME=/opt/android-sdk
ENV ANDROID_SDK="$ANDROID_HOME"
ENV ANDROID_SDK_ROOT="$ANDROID_HOME"
ENV PATH=${PATH}:${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/emulator

#
# Flutter SDK
# https://flutter.dev/docs/development/tools/sdk/releases?tab=linux
ENV FLUTTER_CHANNEL="stable"
ENV FLUTTER_VERSION="3.0.4"
# Set this variable as "enable" to auto config flutter web-server.
# Make sure to use the needed channel and version for this.
ENV FLUTTER_WEB="disable"
ENV FLUTTER_HOME=/opt/flutter
ENV PATH=${PATH}:${FLUTTER_HOME}/bin

# This Dockerfile adds a non-root user with sudo access. Use the "remoteUser"
# property in devcontainer.json to use it. On Linux, the container user's GID/UIDs
# will be updated to match your local UID/GID (when using the dockerFile property).
# See https://aka.ms/vscode-remote/containers/non-root-user for details.
ARG USERNAME=vscode
ARG GROUPNAME=$USERNAME
ARG USER_UID=1000
ARG USER_GID=$USER_UID

#
# Install needed packages, setup user
RUN \
	apt-get update \
	&& DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
		openjdk-17-jdk-headless \
		wget \
		curl \
		git \
		xz-utils \
		zip \
		unzip \
		sudo \
	# Create a non-root user to use if preferred - see https://aka.ms/vscode-remote/containers/non-root-user.
	# [Optional] Add sudo support for the non-root user
	&& groupadd --gid $USER_GID $GROUPNAME \
	&& useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
	&& echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
	&& chmod 0440 /etc/sudoers.d/$USERNAME

#
# Android SDK
WORKDIR /opt
RUN \
	curl -C - --output android-sdk-tools.zip https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_SDK_TOOLS_VERSION}_latest.zip \
	&& mkdir -p ${ANDROID_HOME}/cmdline-tools/ \
	&& unzip -q android-sdk-tools.zip -d ${ANDROID_HOME}/cmdline-tools/ \
	&& mv ${ANDROID_HOME}/cmdline-tools/cmdline-tools ${ANDROID_HOME}/cmdline-tools/latest
RUN \
	yes | sdkmanager --licenses \
	&& sdkmanager platform-tools \
	&& sdkmanager emulator \
	&& sdkmanager "platforms;android-$ANDROID_PLATFORM_VERSION" "build-tools;$ANDROID_BUILD_TOOLS_VERSION"

#
# Flutter SDK
WORKDIR /opt
RUN \
	curl -C - --output flutter.tar.xz https://storage.googleapis.com/flutter_infra_release/releases/${FLUTTER_CHANNEL}/linux/flutter_linux_${FLUTTER_VERSION}-${FLUTTER_CHANNEL}.tar.xz \
	&& tar xf flutter.tar.xz -C .
RUN \
	git config --global --add safe.directory /opt/flutter \
	&& yes | flutter doctor --android-licenses \
	&& flutter config --no-analytics \
	&& if [ "$FLUTTER_WEB" = "enable" ]; then flutter config --enable-web; else flutter config --no-enable-web; fi \
	&& flutter update-packages

# Clean Up
WORKDIR /opt
RUN \
	rm android-sdk-tools.zip flutter.tar.xz \
	&& apt-get autoremove -y \
	&& apt-get clean -y \
	&& rm -rf /var/lib/apt/lists/* \