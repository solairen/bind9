FROM moleszek/ansible:9.0.1

RUN apt-get update && apt-get -y install git openssh-server openssh-client vim

WORKDIR /ansible

COPY * .

ENTRYPOINT [ "bash" ]
