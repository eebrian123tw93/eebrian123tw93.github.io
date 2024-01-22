FROM --platform=linux/amd64 node 

RUN apt-get update && apt-get install sudo
RUN npm install hexo-cli -g


RUN chmod -R 777 /opt

ENV LANG C.UTF-8
ARG TZ
ENV TZ $TZ
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

USER root
WORKDIR /opt