# set base image (host OS)
FROM python:2.7-onbuild

#set the ENV directory in the container
ENV PYTHONPATH /usr/src/app

# define the working dircetory
WORKDIR /app

# copy the dependencies file to the working directory
COPY requirements.txt /app

# add the dependencies file to the working directory
COPY consumer.py /app/

# install dependencies
RUN pip install -r requirements.txt


# command to run on container start
#CMD [ "python", "./src/consumer.py" ,"-p 5672", "-s rabbitmq" ]

