FROM deis/base:latest
MAINTAINER OpDemand <info@opdemand.com>

# update apt
RUN sed -i 's/main$/main universe/' /etc/apt/sources.list && apt-get update

# install prerequisites
RUN apt-get install -y make git libpq-dev python-dev libyaml-dev

# install nginx
RUN apt-get install -y software-properties-common python-software-properties
RUN add-apt-repository -y ppa:nginx/stable
RUN apt-get update
RUN apt-get install -y nginx

# add nginx configuration in
ADD nginx.conf  /etc/nginx/nginx.conf

# install recent pip
RUN wget -qO- https://raw.githubusercontent.com/pypa/pip/1.5.5/contrib/get-pip.py | python -

# create a docs user
RUN useradd -s /bin/bash docs

# install latest etcdctl including no-sync options
RUN wget -q https://s3-us-west-2.amazonaws.com/deis/etcdctl.no-sync -O /usr/local/bin/etcdctl
RUN chmod +x /usr/local/bin/etcdctl

# cache the pip install layers ahead of time
RUN pip install celery==3.1.11 \
                Django==1.6.4 \
                django-allauth==0.15.0 \
                git+https://github.com/deis/django-fsm@add-exception-handling \
                django-guardian==1.1.1 \
                django-json-field==0.5.5 \
                djangorestframework==2.3.13 \
                gunicorn==18.0 \
                psycopg2==2.5.2 \
                python-etcd==0.3.0 \
                PyYAML==3.10 \
                South==0.8.4 \
                docopt==0.6.1 \
                Sphinx>=1.2.2 \
                smartypants>=1.8.3 \
                sphinxcontrib-httpdomain>=1.2.1

# add the current build context to /app
ADD . /app

# install python requirements
RUN pip install -r /app/docs_requirements.txt

# add the controller source for autodocs
RUN git clone https://github.com/deis/deis /deis
RUN mv /deis/controller /controller && rm -rf /deis

# generate the documentation
RUN cd /app && make html

# define the execution environment
WORKDIR /app
CMD ["/app/bin/boot"]
EXPOSE 5000
