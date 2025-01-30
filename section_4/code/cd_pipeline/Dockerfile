FROM centos:7
MAINTAINER Sandro Cirulli <sandro.cirulli@oup.com>

# Instalar dependencias básicas
RUN yum install -y epel-release && \
    yum install -y python-pip java-1.8.0-openjdk-devel docker git && \
    yum clean all

# Configurar entorno Java
ENV JAVA_HOME /usr/lib/jvm/java-1.8.0-openjdk
ENV PATH $PATH:$JAVA_HOME/bin

# Configurar entorno Jenkins
ENV JENKINS_HOME /var/jenkins_home
RUN useradd -d "$JENKINS_HOME" -u 102 -m -s /bin/bash jenkins

# Configurar grupo Docker
RUN groupadd -g 497 docker && \
    usermod -a -G docker jenkins

# Instalar docker-compose (versión compatible)
RUN pip install --no-cache-dir docker-compose==1.25.0

# Directorios persistentes
VOLUME ["/var/jenkins_home", "/rdf", "/tmp", "/storage"]

# Configuración inicial de Jenkins
RUN mkdir -p /usr/share/jenkins/ref/init.groovy.d
COPY init.groovy /usr/share/jenkins/ref/init.groovy.d/tcp-slave-angent-port.groovy

# Descargar Jenkins
ENV JENKINS_VERSION 1.619
RUN curl -L http://mirrors.jenkins-ci.org/war/${JENKINS_VERSION}/jenkins.war -o /usr/share/jenkins/jenkins.war

# Configuraciones adicionales
ENV JENKINS_UC https://updates.jenkins-ci.org
RUN chown -R jenkins "$JENKINS_HOME" /usr/share/jenkins/ref

# Copiar recursos
COPY *.xml /usr/share/jenkins/ref/
COPY updates /usr/share/jenkins/ref/updates
COPY users /usr/share/jenkins/ref/users
COPY rdfunit /usr/local/rdfunit
COPY jenkins.sh /usr/local/bin/jenkins.sh

# Permisos de ejecución
RUN chmod +x /usr/local/bin/jenkins.sh

# Puertos
EXPOSE 8080 50000

USER jenkins

CMD ["/usr/local/bin/jenkins.sh"]