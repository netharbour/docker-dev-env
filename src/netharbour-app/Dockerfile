
# Base image
FROM centos:7.6.1810

MAINTAINER Craig Tomkow "craig.tomkow@bc.net"

ARG UID
ARG GID
ARG NAME

# Timezone
ENV TZ=America/Vancouver

# add netharbour user (used for cron scripts and RRD file permissions)
RUN groupadd -g ${GID} ${NAME} && useradd -s /bin/bash -u ${UID} -g ${GID} ${NAME}

# Install epel first, then ghettoforge
RUN yum install -y epel-release && \
    curl -O http://mirror.ghettoforge.org/distributions/gf/gf-release-latest.gf.el7.noarch.rpm && \
    yum install -y gf-release-latest.gf.el7.noarch.rpm

# Recommended by centos dockerhub team when using minor versions
RUN yum -y update && yum clean all

RUN yum install -y cronie                   \
        gcc                                 \
        httpd                               \
        libssh2                             \
        libssh2-devel                       \
        mod_authz_ldap.x86_64               \
        mod_ssl                             \
        net-snmp                            \
    	net-snmp-utils                      \
    	npm                                 \
    	perl                                \
    	perl-core                           \
    	"perl(Config::Simple)"              \
        "perl(Data::Validate::Domain)"      \
        "perl(Data::Validate::IP)"          \
        "perl(DBD::mysql)"                  \
        "perl(DBI)"                         \
        "perl(JSON)"                        \
        "perl(LWP::Protocol::https)"        \
        "perl(LWP::UserAgent)"              \
        "perl(Net::IP)"                     \
        "perl(Net::SNMP)"                   \
        "perl(NetAddr::IP::Util)"           \
        "perl(Module::Build)"               \
        "perl(Time::HiRes)"                 \
    	php                                 \
    	php-bcmath                          \
    	php-ldap.x86_64                     \
    	php-pear.noarch                     \
    	php-gd.x86_64                       \
    	php-mysql                           \
    	php-soap.x86_64                     \
	    supervisor                          \
    	xorg-x11-fonts-Type1                \
    	rrdtool                             \
    	rrdtool-perl                        \
    	yum --enablerepo=gf-plus install -y postfix3 \
    	&& yum clean all

# install cpanminus
RUN curl -L https://cpanmin.us | perl - --self-upgrade

# install cpan packages that doesn't exist in centos repo
RUN cpanm File::Which
RUN cpanm XML::LibXML
RUN cpanm Net::Netconf

# install time-elapsed benchmarking utility
RUN npm install -g gnomon

# Supervisor setup (multiple process control)
RUN mkdir -p /var/log/supervisor && \
    mkdir -p /etc/supervisor/conf.d

# Server config files
COPY ./system-conf/php.ini /etc/php.ini
COPY ./system-conf/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY ./system-conf/httpd.conf /etc/httpd/conf/httpd.conf
COPY ./system-conf/main.cf /etc/postfix/main.cf
COPY ./system-conf/.htaccess /var/www/html/netharbour/.htaccess

# Cron
# variable substitution for -- flags not supported yet. https://github.com/moby/moby/issues/35018
## for now, statically set uid:gid. Should use vars in future though
COPY --chown=root:root system-conf/crontab.root /var/spool/cron/root
RUN chmod 600 /var/spool/cron/root

# Netharbour App
# variable substitution for -- flags not supported yet. https://github.com/moby/moby/issues/35018
COPY --chown=1000:1000 ./netharbour /var/www/html/netharbour

# RRD volume ownership (create volume and chown it before it's mounted)
# variable substitution for -- flags not supported yet. https://github.com/moby/moby/issues/35018
RUN mkdir -p /opt/netharbour/rrd-files && \
    chown -R 1000:1000 /opt/netharbour

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
