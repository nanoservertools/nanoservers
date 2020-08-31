# escape=`
# ARGs used by FROM statements must before the first FROM:
ARG FromRepo
ARG FromImage
ARG FromTag

FROM ${FromRepo}/${FromImage}:${FromTag} AS base

USER ContainerUser
RUN ECHO "${FromRepo}/${FromImage}:${FromTag}"

FROM ${FromRepo}/${FromImage}:${FromTag} AS build

USER ContainerUser
RUN ECHO "${FromRepo}/${FromImage}:${FromTag}"

ARG NodeVersion
ARG NodePlatform
ARG NodeFolder=node-$NodeVersion`-$NodePlatform
ARG NodeUrl=https://nodejs.org/dist/$NodeVersion`/node-$NodeVersion`-$NodePlatform`.zip

ENV NODE_FOLDER=${NodeFolder}
ENV NODE_URL=${NodeUrl}

WORKDIR C:\nanoservertools

RUN curl -L --silent --show-error `
--location %NODE_URL% `
--output .\nodejs.zip

FROM build as nodeuntar

RUN tar -x -f .\nodejs.zip

FROM nodeuntar as nodemove

WORKDIR C:\nanoservertools

RUN ECHO "C:\nanoservertools\%NODE_FOLDER%" && move "C:\nanoservertools\%NODE_FOLDER%" .\nodejs

FROM nodemove as nodenuke

WORKDIR C:\nanoservertools\nodejs

RUN del npm && del npx && del npx.cmd && ren npm.cmd npm2.cmd

FROM nodenuke as nodeupgrade

RUN .\npm2.cmd install -g npm

FROM nodeupgrade AS noderemoveold

RUN del npm2.cmd

FROM noderemoveold as nodefinal

FROM base as final 

COPY --from=nodefinal C:\nanoservertools\nodejs C:\nanoservertools\nodejs

USER ContainerAdministrator

RUN setx /M PATH "%PATH%;C:\nanoservertools\nodejs"

USER ContainerUser

WORKDIR C:\
