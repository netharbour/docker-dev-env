
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
        httpd                               \
        mod_authz_ldap.x86_64               \
        mod_ssl                             \
    	net-snmp-utils                      \
    	perl                                \
    	"perl(Config::Simple)"              \
        "perl(Data::Validate::Domain)"      \
        "perl(Data::Validate::IP)"          \
        "perl(DBD::mysql)"                  \
        "perl(DBI)"                         \
        "perl(LWP::UserAgent)"              \
        "perl(Net::IP)"                     \
        "perl(Net::SNMP)"                   \
        "perl(NetAddr::IP::Util)"           \
        "perl(Module::Build)"               \
        "perl(Time::HiRes)"                 \
        "perl(XML::LibXML)"                 \
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
COPY --chown=netharbour:netharbour system-conf/crontab.netharbour /var/spool/cron/netharbour
RUN chmod 600 /var/spool/cron/netharbour

# Netharbour App
# variable substitution for -- flags not supported yet. https://github.com/moby/moby/issues/35018
COPY --chown=netharbour:netharbour ./netharbour /var/www/html/netharbour

# RRD volume ownership (create volume and chown it before it's mounted)
# variable substitution for -- flags not supported yet. https://github.com/moby/moby/issues/35018
RUN mkdir -p /opt/netharbour/rrd-files && \
    chown -R netharbour:netharbour /opt/netharbour

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]