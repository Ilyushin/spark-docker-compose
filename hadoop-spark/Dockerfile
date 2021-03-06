FROM teenak/ubuntu-14-04-java8

# Install.
RUN \
  sed -i 's/# \(.*multiverse$\)/\1/g' /etc/apt/sources.list && \
  apt-get update && \
  apt-get -y upgrade && \
  apt-get install -y build-essential && \
  apt-get install -y software-properties-common && \
  apt-get install -y byobu curl git htop man unzip vim wget && \
  apt-get install -y openssh-server sshpass && \
  rm -rf /var/lib/apt/lists/*

RUN mkdir /var/run/sshd
RUN echo 'root:screencast' | chpasswd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed -i 's/#   StrictHostKeyChecking ask/StrictHostKeyChecking no/' /etc/ssh/ssh_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

# Set environment variables.
ENV HOME /root

# Install Java.
#RUN \
#  apt-get update && \
#  echo "deb http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main" | tee /etc/apt/sources.list.d/webupd8team-java.list && \
#  echo "deb-src http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main" | tee -a /etc/apt/sources.list.d/webupd8team-java.list && \
#  apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys EEA14886 && \
#  apt-get update && \
#  cd /var/lib/dpkg/info && \
#  sed -i 's|JAVA_VERSION=8u151|JAVA_VERSION=8u161|' oracle-java8-installer.* && \
#  sed -i 's|PARTNER_URL=http://download.oracle.com/otn-pub/java/jdk/8u151-b12/e758a0de34e24606bca991d704f6dcbf/|PARTNER_URL=http://download.oracle.com/otn-pub/java/jdk/8u161-b12/2f38c3b165be4555a1fa6e98c45e0808/|' oracle-java8-installer.*  && \
#  sed -i 's|SHA256SUM_TGZ="c78200ce409367b296ec39be4427f020e2c585470c4eed01021feada576f027f"|SHA256SUM_TGZ="6dbc56a0e3310b69e91bb64db63a485bd7b6a8083f08e48047276380a0e2021e"|' oracle-java8-installer.* && \
#  sed -i 's|J_DIR=jdk1.8.0_151|J_DIR=jdk1.8.0_161|' oracle-java8-installer.*  && \
#  echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | /usr/bin/debconf-set-selections
#  apt-get install -y oracle-java8-installer && \
#  apt-get install -y oracle-java8-set-default

# Define commonly used JAVA_HOME variable
#ENV JAVA_HOME /usr/lib/jvm/java-8-oracle

# Download Hadoop package
ADD http://apache.mirrors.tds.net/hadoop/common/hadoop-2.7.5/hadoop-2.7.5.tar.gz /tmp/

# Download Spark package
ADD http://d3kbcqa49mib13.cloudfront.net/spark-2.2.0-bin-hadoop2.7.tgz /tmp/

# Create /opt directory
RUN mkdir -p /opt/

# Unpack hadoop into /opt/ and set HADOOP_HOME
RUN tar -xzf /tmp/hadoop-2.7.5.tar.gz -C /opt/

# Copy hadoop confs
ADD /hadoop-conf /opt/hadoop-2.7.5/etc/hadoop/

# Set Hadoop sepcific environment variables
ENV HADOOP_HOME /opt/hadoop-2.7.5
ENV HADOOP_CONF_DIR="${HADOOP_HOME}/etc/hadoop"
ENV HADOOP_LIBEXEC_DIR="${HADOOP_HOME}/libexec"

RUN sed -i 's/export JAVA_HOME=${JAVA_HOME}/export JAVA_HOME=\/usr\/lib\/jvm\/java-8-oracle/' /opt/hadoop-2.7.5/etc/hadoop/hadoop-env.sh

RUN mkdir -p /opt/data/apps/tmp
RUN mkdir -p /opt/data/apps/dfs/name
RUN mkdir -p /opt/data/apps/dfs/data

# Unpack spark into /opt and set SPARK_HOME
RUN tar -xzf /tmp/spark-2.2.0-bin-hadoop2.7.tgz -C /opt/
ENV SPARK_HOME /opt/spark-2.2.0-bin-hadoop2.7

ENV PATH $PATH:${HADOOP_HOME}/sbin/:${HADOOP_HOME}/bin:${SPARK_HOME}/sbin/:${SPARK_HOME}/bin

RUN ssh-keygen -f /root/.ssh/id_rsa -t rsa -N ''

# Spark ports
EXPOSE 7077 6066 8080 8081

# Hdfs ports
EXPOSE 50010 50020 50070 50075 50090 8020 9000
# Mapred ports
EXPOSE 19888
#Yarn ports
EXPOSE 8030 8031 8032 8033 8040 8042 8088
#Other ports
EXPOSE 49707 2122

