FROM rbenavente/hellopython

USER root

#Secret exposed
COPY id_rsa ~/.ssh/id_rsa
COPY evil /evil

#Virus included
COPY eicar ~/eicar.txt
#CMD sed 's/999STANDARD/STANDARD' eicar.txt
#CMD sed -i 's/999STANDARD/STANDARD' ~/eicar.txt

#Update pipc
RUN pip install --upgrade pip

#Expose vulnerable ports
EXPOSE 22
EXPOSE 80


#CMD ["/bin/sh" "-c" "python ./index.py"]
