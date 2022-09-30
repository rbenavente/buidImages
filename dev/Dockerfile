FROM rbenavente/basepython:latest

USER root

#Secret exposed
COPY id_rsa ~/.ssh/id_rsa


#Virus included
COPY evil /evil

#Update pipc
RUN pip install --upgrade pip

#Expose vulnerable ports
EXPOSE 22
EXPOSE 80
