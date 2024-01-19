FROM --platform=linux/amd64 node 


RUN npm install hexo-cli -g


RUN chmod -R 777 /opt

ENV LANG C.UTF-8
ARG TZ
ENV TZ $TZ
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN useradd -s /bin/bash -m brian && echo "brian:brian" | chpasswd && adduser brian sudo
USER brian
WORKDIR /opt